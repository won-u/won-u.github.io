---
layout: post
title: '[Docker] docker 명령어'
comments: true
categories: [Docker]
tags: [docker, tutorial, command]
---

## 사용하기

Docker의 명령은 `docker run`, `docker push`와 같이 `docker <명령>` 형식입니다. 그리고 항상 **root** 권한으로 실행해야 합니다.

먼저 Docker의 기본적인 사용법을 알아보기 위해 Docker Hub에서 제공하는 이미지를 받아서 실행해보겠습니다.

## 이미지 검색

Docker는 [Docker Hub](https://registry.hub.docker.com/)를 통해 이미지를 공유하는 생태계가 구축되어 있습니다. `search` 명령으로 Docker Hub에서 이미지를 검색할 수 있습니다.

```bash
sudo docker search <${image_name}>
```

## 이미지 목록 출력

```bash
sudo docker images
```

## 이미지 받기

```bash
sudo docker pull <${image_name}>
sudo docker pull <${image_name}>:<${tag}>
sudo docker pull <${user_id}>/<${image_name}>:<${tag}>

e.g.)
sudo docker pull ubusudo docker rmi ubuntu:16.04
sudo docker pull ubuntu:latest
sudo docker pull ubuntu:16.04
sudo docker pull ian/ubuntu:16.04
```

## 이미지 컨테이너 생성

```bash
sudo docker run <${option}> <${image_name}> <${exec_file}>

e.g.)
sudo docker run -i -t --name hello_world ubuntu:16.04 /bin/bash
```

### 컨테이너 생성 주요 옵션

```bash
-i # interactive
-t # Allocate a pseudo-TTY
-v # Bind mount a volume
-w # Working directory inside the container
-p # Publish a container's port(s) to the host
-P # Publish all exposed ports to random ports
-h # Container host name
--name # Assign a name to the container
```

## 컨테이너 목록

```bash
sudo docker ps -a
```

## 컨테이너 시작

```bash
sudo docker start <${container_name}>|<${container_id}>

e.g.)
sudo docker start hello_world
```

## 컨테이너 재 시작

```bash
sudo docker restart <${container_name}>|<${container_id}>

e.g.)
sudo docker restart hello_world
```

## 컨테이너 접근

```bash
sudo docker attach <${container_name}>|<${container_id}>

e.g.)
sudo docker attach hello_world
```

/bin/bash 를 실행한 container는 입/출력 모두 가능하지만 DB 혹은 Application 실행 시 출력만 가능

Bash Shell에서 `exit` 또는 `Ctrl+D를` 입력하면 컨테이너가 정지됩니다. 여기서는 `Ctrl+P`, `Ctrl+Q`를 차례대로 입력하여 컨테이너를 정지하지 않고, 컨테이너에서 빠져나옵니다.

## 컨테이너 명령어 실행

```bash
sudo docker exec <${container_name}> <${command}> <${arg...}>

e.g.)
sudo docker exec hello_world echo "Hello World"
sudo docker exec -it hell_world /bin/bash
```

`docker exec <컨테이너 이름> <명령> <매개 변수>` 형식입니다. 컨테이너 이름 대신 컨테이너 ID를 사용해도 됩니다. 컨테이너가 실행되고 있는 상태에서만 사용할 수 있으며 정지된 상태에서는 사용할 수 없습니다.

컨테이너 안의 **echo** 명령을 실행하고 매개 변수로 *“Hello World”*를 지정했기 때문에 *Hello World*가 출력됩니다. `docker exec` 명령은 이미 실행된 컨테이너에 **apt-get**, **yum** 명령으로 패키지를 설치하거나, 각종 데몬을 실행할 때 활용할 수 있습니다.

## 컨테이너 정지

```bash
sudo docker stop <${container_name}>

e.g.)
sudo docker stop hello_world
```

## 컨테이너 삭제

```bash
sudo docker rm <${container_name}>

e.g.)
sudo docker rm hello_world
```

## 이미지 삭제

```bash
sudo docker rmi <${iamage_name}>
sudo docker rmi <${iamage_name}>:<${tag}>
sudo docker rmi <${user_name}>/<${iamage_name}>:<${tag}>

e.g.)
sudo docker rmi ubuntu
sudo docker rmi ubuntu:16.04
sudo docker rmi ian/ubuntu:16.04
```

## 이미지 생성

### Dockerfile

```docker
FROM ubuntu:16.04
MAINTAINER Ian Won <ian.won@obigo.com>

RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx

VOLUME ["/data", "/etc/nginx/site-enabled", "/var/log/nginx"]

WORKDIR /etc/nginx

CMD ["nginx"]

COPY . .
ADD /my/test/data /data

EXPOSE 80
EXPOSE 443

RUN useradd docker_test
RUN passwd -e docker_test
USER docker_test
```

- FROM: 어떤 이미지를 기반으로 할지 설정합니다. Docker 이미지는 기존에 만들어진 이미지를 기반으로 생성합니다. `<이미지 이름>:<태그>` 형식으로 설정합니다.
- MAINTAINER: 메인테이너 정보입니다.
- RUN: Shell 스크립트 혹은 명령을 실행합니다.
    - 이미지 생성 중에는 사용자 입력을 받을 수 없으므로 apt-get install 명령에서 `y` 옵션을 사용합니다(yum install도 동일).
    - 나머지는 nginx 설정입니다.
- VOLUME: 호스트와 공유할 디렉터리 목록입니다. `docker run` 명령에서 `v` 옵션으로 설정할 수 있습니다. 예) `v /root/data:/data` 호스트의 **/root/data** 디렉터리를 Docker 컨테이너의 **/data** 디렉터리에 연결합니다.
- CMD: 컨테이너가 시작되었을 때 실행할 실행 파일 또는 스크립트입니다.
- WORKDIR: CMD에서 설정한 실행 파일이 실행될 디렉터리입니다.
- EXPOSE: 호스트와 연결할 포트 번호입니다.
- COPY: 호스트 파일/디렉토리 를 docker 이미지의 파일시스템으로 복사
- ADD: Copy명령문과 같으며 압축파일, 네트워크상의 파일 등도 가능

### .dockerignore

dockerignore 파일 생성시 Docker 이미지 생성 시 이미지안에 들어가지 않을 파일을 지정 할 수 있습니다.

```docker
.git
*.md
```

## 컨테이너 파일복사

```bash
sudo docker cp <${container_name}>:<${file_path}>/<${file_name}> <${dest_path}>

e.g.)
sudo docker cp hello_world:/data/file/test.sh /home/${USER}/copy
```

`docker cp <컨테이너 이름>:<경로> <호스트 경로>` 형식입니다.

