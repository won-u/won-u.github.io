---
layout: post
title: '[Docker] Docker root dir 변경'
comments: true
categories: [Docker]
tags: [docker]
---

<img data-action="zoom" src="https://won-u.github.io/assets/img/post_image/docker.png" width="70%" height="70%" title="Docker">

## Docker root directory?

Docker를 사용하면 docker hub를 통해 image를 받거나 해당 image를 통해 container를 생성하게 된다. 이런 정보들은 전부 어디에 저장이 되는것일까?  
이런 정보는 <span style="color:indianred">`Docker Root Dir`</span>에 설치가 된다. 해당 위치는 아래와 같이 확인이 가능하다.

```bash
docker info | grep -i "Docker Root Dir"
Docker Root Dir: /var/lib/docker

sudo tree -L 1 /var/lib/docker
/var/lib/docker
├── buildkit
├── containers
├── image
├── network
├── overlay2
├── plugins
├── runtimes
├── swarm
├── tmp
├── trust
└── volumes
```

## Root Dir 변경

### docker service stop
```bash
systemctl stop docker
```

### daemon.json 추가 혹은 수정
```bash
vim /etc/docker/daemon.json
{
    "data-root": "/docker/root/dir"
}
```

### docker service stop
```bash
systemctl start docker
```

