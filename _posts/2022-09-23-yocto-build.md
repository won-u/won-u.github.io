---
layout: post
title: '[Yocto] Yocto 빌드'
comments: true
categories: [Yocto]
tags: [yocto, embedded, programming]
---

## Overview

Yocto를 통한 배포판 빌드를 위한 간단 가이드를 작성합니다.

---

## 요구 사항

빌드 호스트에서는 아래와 같은 요구사항을 충족하여야 합니다.
- 50GB 이상의 남은 공간
- 지원되는 Linux 배포판(예: Fedora, openSUSE, CentOS, Debian 또는 Ubuntu의 최신 릴리스)을 실행합니다. Yocto 프로젝트를 지원하는 Linux 배포판 목록은 Yocto 프로젝트 참조 매뉴얼의 [지원되는 Linux 배포판](https://docs.yoctoproject.org/ref-manual/system-requirements.html#supported-linux-distributions) 섹션을 참조하십시오. 빌드 호스트 준비에 대한 자세한 정보는 Yocto 프로젝트 개발 작업 매뉴얼의 [빌드 호스트 준비](https://docs.yoctoproject.org/dev-manual/start.html#preparing-the-build-host) 섹션을 참조하십시오.
- Git 1.8.3.1 버전 이상
- tar 1.28 버전 이상
- Python 3.6.0 버전 이상
- gcc 7.5 버전 이상
- GNU make 4.0 버전 이상

---

## 빌드 호스트 필요 패키지

빌드 호스트에 필수 호스트 패키지를 설치해야 합니다. 다음 명령은 Ubuntu 배포를 기반으로 호스트 패키지를 설치합니다.

```bash
sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev pylint3 xterm python3-subunit mesa-common-dev zstd liblz4-tool
```

💡 지원되는 모든 Linux 배포판에 대한 호스트 패키지 요구 사항은 Yocto 프로젝트 참조 매뉴얼의 [빌드 호스트에 필요한 패키지](https://docs.yoctoproject.org/ref-manual/system-requirements.html#required-packages-for-the-build-host) 섹션을 참조하십시오.

---

## Poky download

```bash
$ git clone git://git.yoctoproject.org/poky
Cloning into 'poky'...
remote: Counting
objects: 432160, done. remote: Compressing objects: 100%
(102056/102056), done. remote: Total 432160 (delta 323116), reused
432037 (delta 323000) Receiving objects: 100% (432160/432160), 153.81 MiB | 8.54 MiB/s, done.
Resolving deltas: 100% (323116/323116), done.
Checking connectivity... done.
```
릴리스 위키 페이지로 이동하여 최신 안정 릴리스 또는 장기 지원 릴리스에 해당하는 릴리스 코드명(예: langdale)을 선택하십시오.

그런 다음 poky 디렉토리로 이동하여 기존 branch를 살펴봅니다.

```bash
$ cd poky
$ git branch -a
.
.
.
remotes/origin/HEAD -> origin/master
remotes/origin/dunfell
remotes/origin/dunfell-next
.
.
.
remotes/origin/gatesgarth
remotes/origin/gatesgarth-next
.
.
.
remotes/origin/master
remotes/origin/master-next
.
.
.
```
Yocto 프로젝트 관련 리포지토리 액세스에 대한 추가 옵션 및 정보는 Yocto 프로젝트 개발 작업 매뉴얼의 [Yocto 프로젝트 소스 파일 찾기](https://docs.yoctoproject.org/dev-manual/start.html#locating-yocto-project-source-files) 섹션을 참조하십시오.

---

## 이미지 빌드

다음 단계를 사용하여 이미지를 빌드합니다. 빌드 프로세스는 소스에서 툴체인을 포함한 전체 Linux 배포판을 생성합니다.

- 빌드 환경 초기화: poky 디렉토리 내에서 oe-init-build-env 환경 설정 스크립트를 실행하여 빌드 호스트에서 Yocto 프로젝트의 빌드 환경을 정의합니다.  

```bash
$ cd poky
$ source oe-init-build-env
You had no conf/local.conf file. This configuration file has therefore been
created for you with some default values. You may wish to edit it to, for
example, select a different MACHINE (target hardware). See conf/local.conf
for more information as common configuration options are commented.

You had no conf/bblayers.conf file. This configuration file has therefore
been created for you with some default values. To add additional metadata
layers into your configuration please add entries to conf/bblayers.conf.

The Yocto Project has extensive documentation about OE including a reference
manual which can be found at:
    https://docs.yoctoproject.org

For more information about OpenEmbedded see their website:
    https://www.openembedded.org/

### Shell environment set up for builds. ###

You can now run 'bitbake <target>'

Common targets are:
    core-image-minimal
    core-image-full-cmdline
    core-image-sato
    core-image-weston
    meta-toolchain
    meta-ide-support

You can also run generated QEMU images with a command like 'runqemu qemux86-64'

Other commonly useful commands are:
 - 'devtool' and 'recipetool' handle common recipe tasks
 - 'bitbake-layers' handles common layer tasks
 - 'oe-pkgdata-util' handles common target package tasks
```
- 로컬 구성 파일 검사: 빌드 환경을 설정할 때 local.conf라는 로컬 구성 파일을 빌드 디렉토리의 conf 하위 디렉토리에서 사용할 수 있게 됩니다. 이 예에서 기본값은 에뮬레이션에 적합한 qemux86 대상에 대해 빌드하도록 설정됩니다. 사용된 패키지 관리자는 RPM 패키지 관리자로 설정됩니다.
- 빌드: 다음 명령을 사용하여 대상에 대한 OS 이미지를 빌드합니다. 이 예에서는 core-image-sato입니다. 
```bash
$ bitbake core-image-sato
```   
    - bitbake 명령 사용에 대한 정보는 Yocto 프로젝트 개요 및 개념 매뉴얼의 BitBake 섹션을 참조하거나 BitBake 사용자 매뉴얼의 [BitBake 명령](https://docs.yoctoproject.org/bitbake/2.2/bitbake-user-manual/bitbake-user-manual-intro.html#the-bitbake-command)을 참조하십시오.
- QEMU를 사용하여 이미지 시뮬레이션: 이 특정 이미지가 빌드되면 Yocto 프로젝트와 함께 제공되는 빠른 에뮬레이터인 QEMU를 시작할 수 있습니다.
```bash
$ runqemu qemux86-64
```   
    - QEMU 실행에 대한 자세한 내용은 Yocto 프로젝트 개발 작업 매뉴얼의 [QEMU(Quick EMUlator)](https://docs.yoctoproject.org/dev-manual/qemu.html#using-the-quick-emulator-qemu) 사용 장을 참조하십시오.
- QEMU 종료: 종료 아이콘을 클릭하거나 QEMU를 불러온 QEMU 스크립트 창에서 Ctrl-C를 입력하여 QEMU를 종료합니다.

---

## 특정 하드웨어에 대한 사용자 지정

지금까지 에뮬레이션에만 적합한 이미지를 빠르게 구축한 것뿐입니다. 이 섹션에서는 Yocto 프로젝트 개발 환경에 하드웨어 Layer을 추가하여 특정 하드웨어에 맞게 빌드를 사용자 정의하는 방법을 보여줍니다.

일반적으로 레이어는 Yocto 프로젝트에 수행할 작업을 알려주는 관련 지침 및 구성 세트가 포함된 저장소입니다. 관련 메타데이터를 기능적으로 특정 Layer으로 분리하면 모듈식 개발이 용이하고 Layer 메타데이터를 더 쉽게 재사용할 수 있습니다.

💡 규칙에 따라 레이어 이름은 "meta-" 문자열로 시작합니다.


하드웨어 Layer을 추가하려면 다음 단계를 따르십시오.


- Find a layer: 많은 하드웨어 Layer를 사용할 수 있습니다. Yocto 프로젝트 [소스 저장소](https://git.yoctoproject.org/)에는 많은 하드웨어 Layer가 있습니다. 이 예는 [meta-altera](https://github.com/kraj/meta-altera) 하드웨어 Layer을 추가합니다.
- Layer 복제: Git을 사용하여 시스템에서 Layer의 로컬 복사본을 만듭니다. 이전에 생성한 Poky 저장소 복사본의 최상위 수준의 복사본을 넣을 수 있습니다.

```bash
$ cd poky
$ git clone https://github.com/kraj/meta-altera.git
Cloning into 'meta-altera'...
remote: Counting objects: 25170, done.
remote: Compressing objects: 100% (350/350), done.
remote: Total 25170 (delta 645), reused 719 (delta 538), pack-reused 24219
Receiving objects: 100% (25170/25170), 41.02 MiB | 1.64 MiB/s, done.
Resolving deltas: 100% (13385/13385), done.
Checking connectivity... done.
```

이제 하드웨어 Layer는 빌드 호스트의 Poky 참조 repository리 내부의 meta-altera로 사용할 수 있으며 Intel이 소유한 Altera의 하드웨어를 지원하는 데 필요한 모든 메타데이터를 포함합니다.

💡 Layer에는 Yocto 프로젝트 릴리스마다 분기가 있는 것이 좋습니다. 사용 중인 Yocto 프로젝트 릴리스를 지원하는 Layer 브랜치를 확인하십시오.
- 특정 시스템에 대한 구성을 빌드로 변경합니다. local.conf 파일의 MACHINE 변수는 빌드를 위한 시스템을 지정합니다. 이 예에서는 MACHINE 변수를 cyclone5로 설정합니다. 다음 구성이 사용됩니다: [https://github.com/kraj/meta-altera/blob/master/conf/machine/cyclone5.conf](https://github.com/kraj/meta-altera/blob/master/conf/machine/cyclone5.conf).

💡 빌드 구성에 대한 자세한 내용은 앞의 "로컬 구성 파일 검사" 단계를 참조하십시오.

- 레이어 구성 파일에 레이어 추가: 빌드 중에 레이어를 사용하려면 먼저 Build Directory conf 디렉터리에 있는 bblayers.conf 파일에 레이어를 추가해야 합니다.   
bitbake-layers add-layer 명령을 사용하여 구성 파일에 Layer를 추가합니다.   
```bash
$ cd poky/build
$ bitbake-layers add-layer ../meta-altera
NOTE: Starting bitbake server...
Parsing recipes: 100% |##################################################################| Time: 0:00:32
Parsing of 918 .bb files complete (0 cached, 918 parsed). 1401 targets,
123 skipped, 0 masked, 0 errors.
```   
bitbake-layers 스크립트를 사용하여 Layer 추가 섹션에서 Layer 추가에 대한 자세한 정보를 찾을 수 있습니다.

이 단계를 완료하면 Yocto 프로젝트 개발 환경에 meta-altera Layer가 추가되고 cyclone5 머신용으로 빌드하도록 구성되었습니다.   
💡 이전 단계는 데모용입니다. Cyclone5 머신에 대한 이미지를 구축하려는 경우 Altera README를 읽어야 합니다.

---

## 일반 레이어 만들기

격리해야 하는 응용 프로그램이나 특정 동작 집합이 있을 수 있습니다. bitbake-layers create-layer 명령을 사용하여 자신만의 일반 레이어를 생성할 수 있습니다. 이 도구는 layer.conf 구성 파일이 있는 하위 디렉토리, example.bb 레시피가 포함된 recipe-example 하위 디렉토리, 라이선스 파일 및 README를 설정하여 Layer 생성을 자동화합니다.


다음 명령은 도구를 실행하여 poky 디렉토리에 meta-mylayer라는 레이어를 생성합니다.

```bash
$ cd poky
$ bitbake-layers create-layer meta-mylayer
NOTE: Starting bitbake server...
Add your new layer with 'bitbake-layers add-layer meta-mylayer'
```

레이어와 레이어 생성 방법에 대한 자세한 내용은 Yocto 프로젝트 개발 작업 매뉴얼의 [bitbake-layers 스크립트를 사용하여 일반 레이어 생성](https://docs.yoctoproject.org/dev-manual/common-tasks.html#creating-a-general-layer-using-the-bitbake-layers-script) 섹션을 참조하십시오.

---

## 참고

> -- <cite>[https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html](https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html)</cite>
