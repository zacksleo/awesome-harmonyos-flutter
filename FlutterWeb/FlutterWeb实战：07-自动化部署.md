> Flutter Web 开发打包后，可以手动发布到服务器上，通过 nginx 来托管静态页面。本文将介绍如何将这一过程自动化。

## 整体思路

使用脚本自动化构建，然后使用 Docker 打包成镜像，最后部署在服务器上。

## 自动化构建

这里使用 GitLab-CI 来自动化构建。

整个流水线分为四步，分别的 前端构建、Flutter Web 构建、Docker 镜像打包、以及部署。

### 前端构建

```yaml
build-js:
  image: zacksleo/node:19
  stage: .pre
  script: |-
    CI_COMMIT_TAG=${CI_COMMIT_TAG:-latest}
    cd packages/apps/web
    yarn install
    sed -i "s/main.dart.js/main.dart.js?v=$CI_COMMIT_SHORT_SHA/g" src/index.js
    sed -i "s/flutter.js/flutter.js?v=$CI_COMMIT_SHORT_SHA/g" public/index.html
    yarn build
  artifacts:
    paths:
      - packages/apps/web/web
    expire_in: 60 mins
```

### Flutter Web 构建

这里使用了 `flutter build web` 命令来构建 Flutter Web 应用，构建后批量对文件重命名，统一增加 Commit Hash 后缀，以解决缓存问题。

```yaml
build-web:
  stage: build
  script: |-
    CI_COMMIT_TAG=${CI_COMMIT_TAG:-latest}
    echo $(git log -1 --pretty=%s | tail -1) > ./release.log
    cd packages/apps/web
    echo -e '\nHIDE_APP_BAR=true' >> env
    flutter build web --pwa-strategy none --build-number=$VERSION_CODE --build-name=$TAG --web-renderer html --base-href /webapp/
    cp .deploy/flutter.js build/web
    # 对part文件重命名，以解决缓存问题
    sed -i  "s/.part.js/.$CI_COMMIT_SHORT_SHA.part.js/g" build/web/main.dart.js
    sed -i  "s/.part.js/.$CI_COMMIT_SHORT_SHA.part.js/g" build/web/flutter_service_worker.js
    for file in build/web/main.dart.js_* ; do mv $file ${file//part/$CI_COMMIT_SHORT_SHA.part} ; done
  needs: ["build-js"]
  dependencies:
    - build-js
  artifacts:
    paths:
      - packages/apps/web/build/web
      - ./release.log
    expire_in: 120 mins
```

### Docker 镜像打包

这里使用 Docker 来打包镜像，然后推送到 Docker 镜像仓库。打包时，替换了压缩版本的字体图标文件。

Dockerfile 文件配置

```docker
FROM nginx:alpine
COPY .deploy/nginx.conf /etc/nginx/nginx.conf
COPY build/web /usr/share/nginx/html
```

GitLab-CI 文件配置

```yaml
dockerize:
  stage: dockerize
  image: docker:latest
  needs: ["build-web"]
  dependencies:
    - build-web
  before_script: []
  script:
    - if [[ -z "$CI_COMMIT_TAG" ]];then
    -   CI_COMMIT_TAG="latest"
    - fi
    - cd packages/apps/web
    - cp build/web/fonts/MaterialIcons-Regular.otf build/web/assets/fonts
    - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY
    - docker build --build-arg DEPLOY_ENV=$DEPLOY_ENV -t $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG
    - docker rmi $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG || true
```

### 部署

这里使用了 rsync 来同步部署文件到服务器上，然后使用 docker-compose 拉取镜像和来启动服务。

```yaml
prod-web:
  image: zacksleo/node
  stage: release
  needs: ["dockerize"]
  variables:
    DEPLOY_SERVER: "10.10.10.10"
    SSH_PORT: 22
  script:
    - cd packages/apps/web/.deploy
    - CI_COMMIT_TAG=${CI_COMMIT_TAG:-latest}
    - SSH_PORT=${SSH_PORT:-22}
    - rsync -rtvhze "ssh -p $SSH_PORT" . root@$DEPLOY_SERVER:/data/$CI_PROJECT_NAME   --stats
    - ssh -p $SSH_PORT root@$DEPLOY_SERVER "docker login -u gitlab-ci-token -p   $CI_JOB_TOKEN $CI_REGISTRY"
    - ssh -p $SSH_PORT root@$DEPLOY_SERVER "export COMPOSE_HTTP_TIMEOUT=120 && export   DOCKER_CLIENT_TIMEOUT=120 && cd /data/$CI_PROJECT_NAME && echo -e '\nTAG=$CI_COMMIT_TAG' >> .env && docker-compose pull $MODULE && docker-compose   stop $MODULE && docker-compose rm -f $MODULE && docker-compose up -d $MODULE"
  needs: ["dockerize"]
```

## nginx 配置

```lua
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    keepalive_timeout  65;

    gzip  on;
    gzip_min_length 1k;
    gzip_comp_level 5;
    gzip_vary on;
    gzip_static on;
    gzip_types text/plain text/html text/css application/javascript application/x-javascript text/xml application/xml application/xml application/json;

    client_max_body_size 2M;

    server {
      listen 80;
      root /usr/share/nginx/html;
      location /webapp/ {
          rewrite ^/webapp(/.*)$ $1 last;
          index index.html index.htm
          try_files $uri $uri/index.html $uri/ =404;
      }
    }
}

```

- [GitLab CI/CD 入门](https://gitlab.cn/docs/jh/ci/quick_start/)