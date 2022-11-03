---
layout: post
title: '[Docker] Docker HTTP private registry 사용'
comments: true
categories: [Docker]
tags: [docker]
---

<img data-action="zoom" src="https://won-u.github.io/assets/img/post_image/docker.png" width="70%" height="70%" title="Docker">

## Overview

<span style="color:dodgerblue">**Docker private registry**</span>에서 이미지를 pull, push 할 때 <span style="color:indianred">`http: server gave HTTP response to HTTPS client`</span> 에러가 발생을 하였다.

이유는 docker는 registry에 접근할 때 [기본 인증으로 안전하지 않은 레지스트리를 사용할 수 없다.](https://docs.docker.com/registry/insecure/)

하지만 내부망 내에서 사용하던가 정책상 HTTPS 사용이 불가능한 경우에는 HTTP를 사용해야 하는 경우가 있기 때문에 해결 방법이 필요하다.

## 해결 방법

/etc/docker/daemon.json 파일에 아래 내용을 추가 한다.

```bash
$ vim /etc/docker/daemon.json
{
	"insecure-registries": ["registry_ip:5000"]
}
$ systemctl restart docker
```

이후 regitry로 부터 pull을 하면 정상 동작하는 것을 확인 할 수 있다.