---
layout: post
title: '[Docker] docker 설치 방법'
comments: true
categories: [Docker]
tags: [docker, tutorial, install]
---

![Docker](https://won-u.github.io/assets/img/favicons/android-chrome-192x192.png) 
<!-- <a href="#이미지"><img src="https://won-u.github.io/assets/img/favicons/android-chrome-192x192.png" width="40%" height="30%" title="px 설정2" alt="ant_image2"></a> -->

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

