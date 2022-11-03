---
layout: post
title: '[Yocto] Yocto overview'
comments: true
categories: [Yocto]
tags: [yocto, embedded, programming]
---

## Yocto?

임베디드 개발을 하다보면 늘 개발보드의 아키텍쳐는 무엇인가 OS와 부트로더는 어떤것이고 어떠한 버전을 사용할 것인가 매번 확인하고 환경을 만드는데 드는 비용과 시간이 많은것을 느낀다.  
Yocto는 이런 환경을 한번에 셋팅하고 형상관리 되도록 도와주는 시스템이다.

![Yocto overview](https://www.yoctoproject.org/wp-content/uploads/2018/02/yp-diagram-overview.png) 

>Yocto Project는 하드웨어 아키텍처에 관계없이 임베디드 제품에 대한 커스텀 Linux 기반 시스템을 개발자가 작성할 수 있도록 지원하는 오픈 소스 협업 프로젝트입니다. 이 프로젝트에서는 유연한 툴세트와 세계 각지의 임베디드 개발자가 테크놀로지, 소프트웨어 스택, 구성 및 베스트 프랙티스를 공유하여 임베디드 디바이스용으로 맞춤형 Linux 이미지를 작성할 수 있는 공간을 제공합니다.  
>이 프로젝트는 하드웨어 지원 및 소프트웨어 스택을 제공하기 위한 표준을 제공하여 소프트웨어 구성 및 빌드를 교환할 수 있도록 합니다. 사용자는 이 툴을 사용하여 여러 하드웨어 플랫폼 및 소프트웨어 스택에 대한 커스터마이즈를 유지보수 및 확장 가능한 방법으로 구축하고 지원할 수 있습니다.
<!-- >> -- <cite>[https://www.yoctoproject.org/software-overview](https://www.yoctoproject.org/software-overview/)</cite> -->

---

## Yocto 다운로드 및 빌드
``` bash
git clone git://git.yoctoproject.org/poky
cd poky
source oe-init-build-env
bitbake core-image-sato
```

<!-- > -- <cite>[https://docs.yoctoproject.org/](https://docs.yoctoproject.org/)</cite> -->

---

## Yocto project의 기본 구성요소

- **`Configuration Files`** : 변수, 사용자 정의 변수 및 하드웨어 구성 정보의 글로벌 정의를 저장하는 파일입니다. 이들은 빌드 시스템에 특정 플랫폼을 지원하기 위해 무엇을 빌드하고 이미지에 포함시킬지 지시합니다.
- **`Recipe`** : 메타데이터의 가장 일반적인 **형식**입니다.레시피에는 바이너리 이미지를 빌드하기 위해 사용되는 패키지의 설정 및 태스크(설명서) 목록이 포함됩니다. 레시피는 소스 코드를 어디서 얻을 수 있는지와 적용할 패치를 설명합니다. 레시피는 구성 및 컴파일 옵션뿐만 아니라 라이브러리 또는 기타 레시피에 대한 종속성을 설명합니다.이는 Layer로 저장된다.
- **`OpenEmbedded-Core` :** oe-core는 Yocto Project를 포함한 OpenEmbedded에서 파생된 많은 시스템에서 공통되는 기초 레시피, 클래스 및 관련 파일로 구성된 메타데이터입니다. OpenEmbedded 커뮤니티에 의해 개발된 오리지널 저장소의 큐레이션된 서브셋으로, 지속적으로 검증된 소규모 핵심 레시피 세트로 분할되어 엄격하게 관리되고 품질 보증된 핵심 레시피 세트가 생성됩니다.
- **`Poky`**: 레퍼런스 임베디드 디스트리뷰션 및 레퍼런스 테스트 구성에서는 1) 디스트리뷰션의 커스터마이즈 방법, 2) Yocto Project 컴포넌트의 테스트 방법, 3) Poky를 사용하여 Yocto Project를 다운로드하기 위한 수단으로 사용할 수 있습니다. Poky는 제품 수준의 DISTRO가 아니라 커스터마이즈의 출발점이 됩니다. Poky는 oe-core 위에 있는 통합 Layer입니다.
- **`Build system - "Bitbake"`** : 명령(수신) 및 구성 데이터를 해석하는 스케줄러 및 실행 엔진. 종속성 트리를 생성하여 컴파일을 주문하고 포함된 코드의 컴파일을 스케줄링한 후 마지막으로 지정된 커스텀 Linux 이미지(배포)를 빌드합니다. BitBake는 Make 같은 빌드 툴입니다. BitBake 레시피는 특정 패키지의 빌드 방법을 지정합니다. 여기에는 모든 패키지 종속성, 소스 코드 위치, 구성, 컴파일, 빌드, 설치 및 제거 지침이 포함됩니다. 레시피는 패키지의 메타데이터도 표준 변수에 저장합니다.관련 레시피가 층으로 통합됩니다. 빌드 프로세스 중에 의존관계가 추적되고 패키지의 네이티브 또는 크로스 컴파일이 수행됩니다. 크로스 빌드 설정의 첫 번째 단계로 프레임워크는 타깃 플랫폼에 적합한 크로스 컴파일러 툴 체인(Extensible SDK)을 작성하려고 합니다.
- **`Package`**: 빌드 시스템의 출력인 최종 이미지 파일.
- **`Extensible Software Development Kit (ESDK)`**: 다른 애플리케이션 개발자가 코드를 사용할 수 있도록 라이브러리 및 프로그래밍 변경 사항을 이미지에 통합할 수 있는 애플리케이션 개발자를 위한 커스텀 SDK입니다. Cross-compiler, 빌드 출력 패키지 등 모든게 포함되어 있습니다.
- **`Image`**: 디바이스에 로드하기 위한 Linux 이미지

---

## Yocto 워크플로우

![Yocto workflow](https://www.yoctoproject.org/wp-content/uploads/2017/07/yp-how-it-works-new-diagram.png) 

1. 개발자는 아키텍처, 정책, 패치 및 구성 세부사항을 지정합니다.
2. 빌드 시스템은 지정된 위치에서 소스 코드를 가져와 다운로드합니다. 이 프로젝트는 tarball과 같은 표준 메서드나 git과 같은 소스 코드 저장소 시스템을 지원합니다.
3. 다운로드가 완료되면 소스가 패치가 적용되는 로컬워크 영역으로 추출되어 소프트웨어의 구성 및 컴파일을 위한 일반적인 절차가 실행됩니다.
4. 소프트웨어는 선택한 바이너리 패키지 형식(deb, rpm 또는 ipk)을 사용하여 소프트웨어를 롤업하는 임시 스테이징 영역에 설치됩니다.
5. 빌드 프로세스 전체에서 다양한 QA 및 건전성 검사가 실행됩니다.
6. 바이너리가 생성되면 바이너리 패키지 피드가 생성되어 최종 루트 파일이미지 작성에 사용됩니다.
7. 파일 시스템 이미지가 생성됩니다.

---


## 참고

> -- <cite>[https://www.yoctoproject.org/](https://www.yoctoproject.org/)</cite>
