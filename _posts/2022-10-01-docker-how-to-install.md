---
layout: post
title: '[Docker] Docker 설치 방법'
comments: true
categories: [Docker]
tags: [docker]
---

<img data-action="zoom" src="https://won-u.github.io/assets/img/post_image/docker.png" width="70%" height="70%" title="Docker">

## 설치 스크립트를 통한 설치

Docker는 리눅스 배포판 종류를 자동으로 인식하여 패키지를 설치해주는 스크립트를 제공합니다.

```bash
sudo wget -qO- https://get.docker.com/ | sh
```

## 우분투 apt를 통한 설치

```bash
sudo apt-get update
sudo apt-get install docker.io
```

docker 서비스 실행

```bash
sudo service docker start
```

부팅했을 때 자동으로 실행하기

```bash
sudo chkconfig docker on
```

docker 사용 권한

```bash
sudo usermod -aG docker $USER
```
