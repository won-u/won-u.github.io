---
layout: post
title: '[Raspberrypi4] Raspberrypi4 에서 vscode server 실행'
comments: true
categories: [raspberrypi4]
tags: [raspberrypi4]
---

## Overview
ipad를 통해 개발을 해볼까 하는 생각을 가지고 있었는데 vscode server를 설치하여 ipad에서 vscode를 사용 할 수 있다는것을 알게 되었다.

다행히도 집에 raspberrypi4가 놀고 있어서 code server를 오픈하고 sub domain을 주면 외부 어디서든 vscode를 사용 할 수 있다!!

docker image를 처음부터 만드려다 발견한 **[code-server docker hub](https://hub.docker.com/r/linuxserver/code-server)** 쉽게 사용 할 수 있을 것 같다.


## vscode server 설치

### Image pull
```bash
docker pull linuxserver/code-server
```

### Container 실행
```bash
docker run -d \
  --name=${code_server_container_name} \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Seoul \
  -e PASSWORD=password `#optional` \
  -e DEFAULT_WORKSPACE=/config/workspace `#optional` \
  -p 8443:8443 \
  -v ${CONFIG_DIR}:/config \
  --restart unless-stopped \
  linuxserver/code-server:latest
```

### code server 접근
```html
http://${raspberrypi4_ip}:8443
```
![codeserver](/assets/img/post_image/codeserver.png)
