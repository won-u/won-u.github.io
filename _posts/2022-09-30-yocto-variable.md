---
layout: post
title: '[Yocto] Yocto variable glossary (사용 빈도수 높은것만)'
comments: true
categories: [Yocto]
tags: [yocto, programming, tutorial]
---
## ALLOW_EMPTY

출력 패키지가 비어 있는 경우에도 출력 패키지를 생성할지 여부를 지정합니다. 기본적으로 BitBake는 빈 패키지를 생성하지 않습니다. 이 기본 동작은 RDEPENDS 또는 패키지 존재에 대한 기타 하드 런타임 요구 사항이 있는 경우 문제를 일으킬 수 있습니다.

```bash
ALLOW_EMPTY:${PN} = "1"
ALLOW_EMPTY:${PN}-dev = "1"
ALLOW_EMPTY:${PN}-staticdev = "1"
```

## AUTOREV

SRCREV가 이 변수의 값으로 설정되면 저장소의 최신 소스 리비전을 사용하도록 지정됩니다. 다음은 한 가지 예입니다.

```bash
SRCREV = "${AUTOREV}"
```

## B

OpenEmbedded 빌드 시스템이 레시피의 빌드 프로세스 중에 생성된 개체를 배치하는 빌드 디렉토리 내의 디렉토리입니다. 기본적으로 이 디렉토리는 다음과 같이 정의된 S 디렉토리와 동일합니다.

```bash
S = "${WORKDIR}/${BP}"
```

## BBCLASSEXTEND

- 하나의 레시피에서 native용(e.g., x86_64)과 target용(e.g., aarch64) 둘다 만들 수 있다.
    - target architecture은 현재 환경의 **MACHINE**에 영향을 받는다.
    - native용으로 레시피를 따로 만드는 것은 매우 낭비인데, Yocto에서는 *`native.bbclass`* 를 사용해, native용으로 레시피를 따로 만들지 않아도 되게한다.
    - `BBCLASSEXTEND = "native"`만 레시피에 써주자.

```bash
BBCLASSEXTEND =+ "native nativesdk"
BBCLASSEXTEND =+ "multilib:multilib_name"
```

## BBLAYERS

빌드 중에 활성화할 레이어를 나열합니다. 이 변수는 빌드 디렉토리의 `bblayers.conf` 구성 파일에 정의되어 있습니다. 다음은 예입니다.

```bash
BBLAYERS = " \
    /home/scottrif/poky/meta \
    /home/scottrif/poky/meta-poky \
    /home/scottrif/poky/meta-yocto-bsp \
    /home/scottrif/poky/meta-mykernel \
    "
```

## BBMASK

BitBake가 레시피 및 레시피 추가 파일을 처리하는 것을 방지합니다.
BBMASK 변수를 사용하여 이러한 .bb 및 .bbappend 파일을 숨길 수 있습니다. BitBake는 표현식과 일치하는 레시피 또는 레시피 추가 파일을 무시합니다. BitBake가 그것들을 전혀 보지 못하는 것과 같습니다. 결과적으로 일치하는 파일은 구문 분석되거나 BitBake에서 사용되지 않습니다.

다음 예는 완전한 정규식을 사용하여 BitBake가 `meta-ti/recipes-misc/` 디렉토리에 있는 모든 레시피 및 레시피 추가 파일을 무시하도록 지시합니다.

```bash
BBMASK = "meta-ti/recipes-misc/"
```

여러 디렉터리 또는 레시피를 마스킹하려면 여러 정규식 조각을 지정할 수 있습니다. 다음 예는 여러 디렉토리와 개별 레시피를 마스킹합니다.

```bash
BBMASK += "/meta-ti/recipes-misc/ meta-ti/recipes-ti/packagegroup/"
BBMASK += "/meta-oe/recipes-support/"
BBMASK += "/meta-foo/.*/openldap"
BBMASK += "opencv.*\.bbappend"
BBMASK += "lzma"
```

💡 디렉토리 이름을 지정할 때 후행 슬래시 문자를 사용하여 해당 디렉토리 이름과 일치하는지 확인하십시오.


## BP

기본 레시피 이름과 버전이지만 특별한 레시피 이름 접미사는 없습니다(예: -native, lib64- 등). BP는 다음으로 구성됩니다.

```bash
${BPN}-${PV}
```

## BPN

이 변수는 nativesdk-, -cross, -native 및 multilib의 lib64- 및 lib32-와 같이 공통 접두사 및 접미사가 제거된 PN 변수 버전입니다.

제거된 접두사 및 접미사의 정확한 목록은 각각 MLPREFIX 및 SPECIAL_PKGSUFFIX 변수에 의해 지정됩니다.

## BUILDDIR

빌드 디렉토리의 위치를 가리킵니다. 스크립트를 실행할 때 빌드 디렉토리 경로를 전달하여 oe-init-build-env 스크립트를 통해 이 디렉토리를 간접적으로 정의할 수 있습니다.

스크립트를 실행하고 빌드 디렉토리 경로를 제공하지 않으면 BUILDDIR은 기본적으로 현재 디렉토리에서 빌드합니다.

## D

대상 디렉토리. do_install 태스크에 의해 구성요소가 설치되는 빌드 디렉토리의 위치입니다. 이 위치의 기본값은 다음과 같습니다.

```bash
${WORKDIR}/image
```

💡 이 디렉토리에서 읽거나 쓰는 작업은 fakeroot에서 실행되어야 합니다.  


## DEPENDS

레시피의 빌드 시간 종속성을 나열합니다. 이는 빌드 시 레시피에 콘텐츠(예: 헤더 및 공유 라이브러리)가 필요한 다른 레시피에 대한 종속성입니다.
예를 들어 다음 할당을 포함하는 레시피 foo를 고려하십시오.

```bash
DEPENDS = "bar"
```

이전 할당의 실질적인 효과는 foo에 대한 do_configure 작업이 실행될 때 STAGING_DIR* 변수에 의해 제공된 적절한 스테이징 시스템 루트에서 bar로 설치된 모든 파일을 사용할 수 있다는 것입니다. 이 메커니즘은 기본 클래스의 [deptask] 선언을 통해 DEPENDS에 나열된 각 레시피의 do_populate_sysroot 작업에 do_configure가 종속되도록 하여 구현됩니다.

💡 예를 들어 STAGING_DIR_HOST를 명시적으로 참조할 필요는 거의 없습니다. 표준 클래스 및 빌드 관련 변수는 적절한 스테이징 시스템 루트를 자동으로 사용하도록 구성됩니다.


## DEPLOY_DIR

OpenEmbedded 빌드 시스템이 빌드 시스템 외부에서 사용할 준비가 된 이미지, 패키지, SDK 및 기타 출력 파일을 배치하는 데 사용하는 일반 영역을 가리킵니다. 기본적으로 이 디렉토리는 `${TMPDIR}/deploy`로 [**빌드 디렉토리**](https://docs.yoctoproject.org/ref-manual/terms.html#term-Build-Directory) 내에 있습니다.
빌드 디렉토리의 구조에 대한 자세한 정보는 "[**빌드 디렉토리 - build**/](https://docs.yoctoproject.org/ref-manual/structure.html#the-build-directory-build)" 섹션을 참조하십시오. 배포 디렉토리의 내용에 대한 자세한 내용은 Yocto 프로젝트 개요 및 개념 매뉴얼의 "[**이미지**](https://docs.yoctoproject.org/overview-manual/concepts.html#images)", "[**패키지 피드**](https://docs.yoctoproject.org/overview-manual/concepts.html#package-feeds)" 및 "[**애플리케이션 개발 SDK**](https://docs.yoctoproject.org/overview-manual/concepts.html#application-development-sdk)" 섹션을 모두 참조하십시오.

## DEPLOY_DIR_IMAGE

OpenEmbedded 빌드 시스템이 대상 시스템에 배포할 준비가 된 이미지 및 기타 관련 출력 파일을 배치하는 데 사용하는 영역을 가리킵니다. 디렉토리는 `${MACHINE}` 이름을 포함하므로 시스템에 따라 다릅니다. 기본적으로 이 디렉토리는 `${DEPLOY_DIR}/images/${MACHINE}/`로 [**빌드 디렉토리**](https://docs.yoctoproject.org/ref-manual/terms.html#term-Build-Directory)에 있습니다.
파일을 배포할 때 레시피에서 직접 사용해서는 안 됩니다. 대신, 레시피가 의존성에 의해 이미 배포된 파일을 "읽어야" 할 때만 유용합니다. 따라서 배포 클래스로 [**DEPLOYDIR**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DEPLOYDIR)의 내용을 채우거나 **[image](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-image)** class에서 [**IMGDEPLOYDIR**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMGDEPLOYDIR)의 내용으로 채워야 합니다.
빌드 디렉토리의 구조에 대한 자세한 정보는 "[빌드 디렉토리 - build/](https://docs.yoctoproject.org/ref-manual/structure.html#the-build-directory-build)" 섹션을 참조하십시오. 배포 디렉토리의 내용에 대한 자세한 내용은 Yocto 프로젝트 개요 및 개념 매뉴얼의 "[**이미지**](https://docs.yoctoproject.org/overview-manual/concepts.html#images)" 및 "[**애플리케이션 개발 SDK**](https://docs.yoctoproject.org/overview-manual/concepts.html#application-development-sdk)" 섹션을 참조하십시오.

## DEPLOYDIR

배포 클래스를 상속할 때 DEPLOYDIR은 다음과 같이 배포 클래스에 설정된 배포된 파일의 임시 작업 영역을 가리킵니다.

```bash
DEPLOYDIR = "${WORKDIR}/deploy-${PN}"
```

배포 클래스를 상속하는 레시피는 배포할 파일을 DEPLOYDIR에 복사해야 하며 나중에 클래스가 파일을 DEPLOY_DIR_IMAGE에 복사합니다.

## DISTRO

배포의 짧은 이름입니다. 배포의 긴 이름에 대한 정보는 [**DISTRO_NAME**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTRO_NAME) 변수를 참조하십시오.
[**DISTRO**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTRO) 변수는 루트 이름이 변수의 인수와 동일하고 파일 이름 확장자가 .conf인 배포 구성 파일에 해당합니다. 예를 들어, Poky 배포에 대한 배포 구성 파일의 이름은 `poky.conf`이고 **[소스 디렉터리](https://docs.yoctoproject.org/ref-manual/terms.html#term-Source-Directory)**의 meta-`poky/conf/distro` 디렉터리에 있습니다.
해당 `poky.conf` 파일 내에서 **[DISTRO](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTRO)** 변수는 다음과 같이 설정됩니다.

```bash
DISTRO = "poky"
```

배포 구성 파일은 배포 구성이 포함된 메타데이터 내의 `conf/distro` 디렉토리에 있습니다. DISTRO 값은 공백을 포함할 수 없으며 일반적으로 모두 소문자입니다.

💡 [**DISTRO**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTRO) 변수가 비어 있으면 기본 구성 세트가 사용되며 이는 소스 디렉토리의 `meta/conf/distro/defaultsetup.conf`에도 지정됩니다.


## DISTRO_FEATURES

다양한 기능에 대한 배포에서 원하는 소프트웨어 지원. 배포 구성 파일에서 배포 기능을 정의합니다.
대부분의 경우 **[DISTRO_FEATURES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTRO_FEATURES)**에 기능이 있는지 여부는 기능을 선택적으로 지원하는 레시피에 대한 **[do_configure](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-configure)** 작업 중에 구성 스크립트에 제공된 적절한 옵션으로 변환됩니다. 예를 들어 **[DISTRO_FEATURES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTRO_FEATURES)**에 "x11"을 지정하면 선택적으로 X11을 지원할 수 있는 대상용으로 구축된 모든 소프트웨어에서 X11 지원이 활성화됩니다.
두 가지 더 많은 예는 Bluetooth 및 NFS 지원입니다. Yocto 프로젝트와 함께 제공되고 이 변수와 함께 제공할 수 있는 기능의 전체 목록은 "**[Distro Featrues](https://docs.yoctoproject.org/ref-manual/features.html#ref-features-distro)**" 섹션을 참조하세요.

## EXTRA_OECMAKE

추가 **[CMake](https://cmake.org/overview/)** 옵션. 추가 정보는 **[cmake](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-cmake)** 클래스를 참조하십시오.

## EXTRA_OECONF

추가 구성 스크립트 옵션. 구성 스크립트 옵션 전달에 대한 추가 정보는 **[PACKAGECONFIG_CONFARGS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG_CONFARGS)**를 참조하십시오.

## EXTRA_OEMAKE

추가 GNU make 옵션.
**[EXTRA_OEMAKE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-EXTRA_OEMAKE)**의 기본값은 ""이므로 필요한 GNU 옵션을 지정하려면 변수를 설정해야 합니다.
**[PARALLEL_MAKE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PARALLEL_MAKE)** 및 **[PARALLEL_MAKEINST](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PARALLEL_MAKEINST)**도 **[EXTRA_OEMAKE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-EXTRA_OEMAKE)**를 사용하여 필요한 플래그를 전달합니다.

## EXTRA_USERS_PARAMS

**[extrausers](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-extrausers)** 클래스를 상속할 때 이 변수는 이미지 수준의 사용자 및 그룹 작업을 제공합니다. 이것은 사용자 및 그룹 구성을 특정 레시피에 연결하는 useradd 클래스를 사용하는 것과 비교하여 사용자 및 그룹 구성을 제공하는 보다 전역적인 방법입니다.
**[EXTRA_USERS_PARAMS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-EXTRA_USERS_PARAMS)**를 사용하여 구성할 수 있는 명령 세트 목록은 **extrausers** 클래스에 표시됩니다. 다음 명령은 동일한 이름의 일반 Unix 명령에 매핑됩니다.

```bash
EXTRA_USERS_PARAMS = "\
useradd -p '' tester; \
groupadd developers; \
userdel nobody; \
groupdel -g video; \
groupmod -g 1020 developers; \
usermod -s /bin/sh tester; \
"
```

### EXTRA_USERS_PARAMS example

```bash
INHERIT:append = " extrausers"
EXTRA_IMAGE_FEATURES:append = " \
    allow-empty-password \
"
# User account has been included in the adm group. this is to allow access to files in /var/log
EXTRA_USERS_PARAMS:append = " \
    useradd -d /home/${@d.getVar('USER_ACCOUNT')} -p '' ${@d.getVar('USER_ACCOUNT')}; \
    usermod -s /bin/sh -aG adm ${@d.getVar('USER_ACCOUNT')}; \
"
```

```bash
INHERIT:append = " extrausers"
EXTRA_USERS_PARAMS:append = " \
    usermod -s /sbin/nologin root; \
"
```

## FILES

패키지에 배치된 파일 및 디렉터리 목록입니다. 그 PAGACKES 변수는 패키지를 나열합니다. 레시피에 의해 생성된. FILES 변수를 사용하려면 패키지 이름 재정의를 제공하십시오. 결과 패키지를 식별합니다. 그런 다음 공백으로 구분하여 입력합니다. 포함할 파일을 식별하는 파일 또는 경로 목록 결과 패키지의 일부입니다. 다음은 한 가지 예입니다.

```bash
FILES:${PN} += "${bindir}/mydir1 ${bindir}/mydir2/myfile"
```

💡 파일이나 경로를 지정할 때 Python의 **[glob](https://docs.python.org/3/library/glob.html)** 구문을 사용하여 패턴 일치를 지정할 수 있습니다. 구문에 대한 자세한 내용은 이전 링크를 따라 설명서를 참조하십시오.  


💡 **[FILES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-FILES)** 변수의 일부로 경로를 지정할 때 적절한 경로 변수를 사용하는 것이 좋습니다. 예를 들어, `/etc` 대신 `${sysconfdir}`을 사용하거나 `/usr/bin` 대신 `${bindir}`을 사용합니다. **[소스 디렉토리](https://docs.yoctoproject.org/ref-manual/terms.html#term-Source-Directory)**의 `meta/conf/bitbake.conf` 파일 상단에서 이러한 변수 목록을 찾을 수 있습니다. 또한 이 파일에서 다양한 FILES:* 변수의 기본값을 찾을 수 있습니다.


**[FILES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-FILES)** 변수와 함께 제공한 파일 중 일부가 편집 가능하고 패키지 관리 시스템(PMS)에서 패키지 업데이트 프로세스 동안 덮어쓰지 않아야 한다는 것을 알고 있는 경우 PMS가 덮어쓰지 않도록 이러한 파일을 식별할 수 있습니다. PMS에서 이러한 파일을 식별하는 방법에 대한 정보는 **[CONFFILES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-CONFFILES)** 변수를 참조하십시오.

## FILEEXTRAPATHS

OpenEmbedded 빌드 시스템이 레시피를 처리하고 파일을 추가할 때 파일과 패치를 찾을 때 사용하는 검색 경로를 확장합니다. BitBake가 레시피를 처리할 때 사용하는 기본 디렉토리는 처음에 FILESPATH 변수에 의해 정의됩니다. FILESEXTRAPATHS를 사용하여 FILESPATH 변수를 확장할 수 있습니다.
모범 사례에 따르면 .bbappend 파일 내에서 FILESEXTRAPATHS를 사용하고 다음과 같이 경로를 앞에 추가합니다.

```bash
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
```

위의 예에서 빌드 시스템은 먼저 해당 추가 파일과 동일한 이름을 가진 디렉토리에서 파일을 찾습니다.

💡 **[FILESEXTRAPATHS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-FILESEXTRAPATHS)**를 확장할 때 즉시 확장(:=) 연산자를 사용해야 합니다. 즉시 확장은 확장으로 인해 필요한 파일이 포함되지 않은 디렉토리가 발생할 수 있는 나중에가 아니라 지시문이 발생할 때 BitBake가 **[THISDIR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-THISDIR)**를 평가하도록 합니다.
또한 앞에 추가하는 경우 후행 분리 콜론 문자를 포함하십시오. 검색 경로 앞에 디렉토리를 추가하여 경로를 확장하도록 BitBake에 지시하기 때문에 후행 콜론 문자가 필요합니다.


또 다른 일반적인 용도는 다음과 같습니다.

```bash
FILESEXTRAPATHS:prepend := "${THISDIR}/files:"
```

이 예에서 빌드 시스템은 FILESPATH 변수를 확장하여 해당 추가 파일과 동일한 디렉토리에 있는 files라는 디렉토리를 포함합니다.
다음 예에서는 구체적으로 세 가지 경로를 추가합니다.

```bash
FILESEXTRAPATHS:prepend := "path_1:path_2:path_3:"
```

마지막 예는 검색 경로를 확장하고 BSP 계층에서 유용한 MACHINE 특정 재정의를 포함하는 방법을 보여줍니다.

```bash
FILESEXTRAPATHS:prepend:intel-x86-common := "${THISDIR}/${PN}:"
```

## HOSTTOOLS

빌드 작업 내에서 호출할 수 있어야 하는 빌드 호스트의 도구 목록(필터)은 공백으로 구분됩니다. 이 필터를 사용하면 호스트 오염 가능성을 줄이는 데 도움이 됩니다. **[HOSTTOOLS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-HOSTTOOLS)** 값에 지정된 도구가 빌드 호스트에서 발견되지 않으면 OpenEmbedded 빌드 시스템에서 오류가 발생하고 빌드가 시작되지 않습니다.
추가 정보는 **[HOSTTOOLS_NONFATAL](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-HOSTTOOLS_NONFATAL)**을 참조하십시오.

## IMAGE_CLASSES

모든 이미지가 상속해야 하는 클래스 목록입니다. 일반적으로 이 변수를 사용하여 OpenEmbedded 빌드 시스템이 생성하는 다양한 유형의 이미지를 등록하는 클래스 목록을 지정합니다.
**[IMAGE_CLASSES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_CLASSES)**의 기본값은 `image_types`입니다. 이 변수는 `local.conf` 또는 배포 구성 파일에서 설정할 수 있습니다.
자세한 내용은 **[소스 디렉토리](https://docs.yoctoproject.org/ref-manual/terms.html#term-Source-Directory)**의 `meta/classes/image_types.bbclass`를 참조하십시오.

## IMAGE_FEATURES

이미지에 포함할 기본 기능 목록입니다. 일반적으로 이미지 레시피에서 이 변수를 구성합니다. Build Directory에 있는 local.conf 파일에서 이 변수를 사용할 수 있지만 모범 사례에서는 사용하지 않는 것이 좋습니다.

💡 To enable extra features from outside the image recipe, use the [EXTRA_IMAGE_FEATURES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-EXTRA_IMAGE_FEATURES) variable.


Yocto 프로젝트와 함께 제공되는 이미지 기능 목록은 "**[Image Featrues](https://docs.yoctoproject.org/ref-manual/features.html#ref-features-image)**" 섹션을 참조하세요.
이 변수를 사용하여 이미지를 사용자 정의하는 방법을 보여주는 예제는 Yocto 프로젝트 개발 작업 매뉴얼의 “**[Customizing Images Using Custom IMAGE_FEATURES and EXTRA_IMAGE_FEATURES](https://docs.yoctoproject.org/dev-manual/common-tasks.html#customizing-images-using-custom-image-features-and-extra-image-features)**” 섹션을 참조하십시오.

## IMAGE_FSTYPES

루트 파일 시스템을 생성할 때 빌드 중에 OpenEmbedded 빌드 시스템이 사용하는 형식을 지정합니다. 예를 들어, IMAGE_FSTYPES를 다음과 같이 설정하면 빌드 시스템이 .ext3 및 .tar.bz2의 두 가지 형식을 사용하여 루트 파일 시스템을 생성합니다.

```bash
IMAGE_FSTYPES = "ext3 tar.bz2"
```

선택할 수 있는 지원되는 이미지 형식의 전체 목록은 [**IMAGE_TYPES**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_TYPES)를 참조하세요.

💡 이미지 레시피가 "inherit image” 행을 사용하고 레시피 내에서 [**IMAGE_FSTYPES**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_FSTYPES)를 설정하는 경우 "이미지 상속" 행을 사용하기 전에 [**IMAGE_FSTYPES**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_FSTYPES)를 설정해야 합니다.
OpenEmbedded 빌드 시스템이 이 변수를 처리하는 방식으로 인해 :append 또는 :prepend를 사용하여 내용을 업데이트할 수 없습니다. [**IMAGE_FSTYPES**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_FSTYPES) 변수에 하나 이상의 옵션을 추가하려면 += 연산자를 사용해야 합니다.


## IMAGE_INSTALL

이미지 클래스를 통해 이미지에 설치할 패키지를 지정하기 위해 레시피에서 사용합니다. 순서 문제를 피하기 위해 [**IMAGE_INSTALL**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_INSTALL) 변수를 주의해서 사용하십시오.
이미지 레시피는 [**image.bbclass**](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-image)를 통해 이미지에 설치할 패키지를 지정하도록 [**IMAGE_INSTALL**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_INSTALL)을 설정합니다. 또한 [IMAGE_FEATURES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_FEATURES)와 함께 사용되는 목록을 기본 내용과 함께 [**IMAGE_INSTALL**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_INSTALL)에서 자동 생성 항목으로 전환할 수 있는 [**core-image**](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-core-image) 클래스와 같은 "helper" 클래스가 있습니다.

이 변수를 사용할 때는 다음과 같이 사용하는 것이 가장 좋습니다.

```bash
IMAGE_INSTALL:append = " package-name"
```

따옴표 문자와 패키지 이름의 시작 부분 사이에 공백을 포함해야 합니다.

## IMAGE_POSTPROCESS_COMMAND

OpenEmbedded 빌드 시스템이 최종 이미지 출력 파일을 생성하면 호출할 함수 목록을 지정합니다. 세미콜론으로 구분된 함수를 지정할 수 있습니다.

```bash
IMAGE_POSTPROCESS_COMMAND += "function; ... "
```

함수 내의 명령에 루트 파일 시스템 경로를 전달해야 하는 경우 루트 파일 시스템 이미지가 되는 디렉토리를 가리키는 `${IMAGE_ROOTFS}`를 사용할 수 있습니다. 자세한 내용은 **[IMAGE_ROOTFS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_ROOTFS)** 변수를 참조하십시오.

## IMAGE_PREPROCESS_COMMAND

OpenEmbedded 빌드 시스템이 최종 이미지 출력 파일을 생성하기 전에 호출할 함수 목록을 지정합니다. 세미콜론으로 구분된 함수를 지정할 수 있습니다.

```bash
IMAGE_PREPROCESS_COMMAND += "function; ... "
```

함수 내의 명령에 루트 파일 시스템 경로를 전달해야 하는 경우 루트 파일 시스템 이미지가 되는 디렉토리를 가리키는 `${IMAGE_ROOTFS}`를 사용할 수 있습니다. 자세한 내용은 **[IMAGE_ROOTFS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_ROOTFS)** 변수를 참조하십시오.

## IMAGE_ROOTFS

루트 파일 시스템이 구성 중일 때(예: **[do_rootfs](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-rootfs)** 작업 중) 위치. 이 변수는 구성할 수 없습니다. 변경하지 마십시오.

## IMAGE_TYPES

Specifies the complete list of supported image types by default:

- btrfs
- container
- cpio
- cpio.gz
- cpio.lz4
- cpio.lzma
- cpio.xz
- cramfs
- erofs
- erofs-lz4
- erofs-lz4hc
- ext2
- ext2.bz2
- ext2.gz
- ext2.lzma
- ext3
- ext3.gz
- ext4
- ext4.gz
- f2fs
- hddimg
- iso
- jffs2
- jffs2.sum
- multiubi
- squashfs
- squashfs-lz4
- squashfs-lzo
- squashfs-xz
- tar
- tar.bz2
- tar.gz
- tar.lz4
- tar.xz
- tar.zst
- ubi
- ubifs
- wic
- wic.bz2
- wic.gz
- wic.lzma

For more information about these types of images, see `meta/classes/image_types*.bbclass`
 in the [**Source Directory**](https://docs.yoctoproject.org/ref-manual/terms.html#term-Source-Directory)
.

## IMGDEPLOYDIR

이미지 클래스를 직접 또는 [**core-image**](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-core-image) 클래스를 통해 상속할 때 [**IMGDEPLOYDIR**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMGDEPLOYDIR)은 다음과 같이 이미지 클래스에 설정된 배포된 파일의 임시 작업 영역을 가리킵니다.

```bash
IMGDEPLOYDIR = "${WORKDIR}/deploy-${PN}-image-complete"
```

이미지 클래스를 상속하는 레시피는 IMGDEPLOYDIR에 배포할 파일을 복사해야 하며, 나중에 클래스가 해당 파일을 **[DEPLOY_DIR_IMAGE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DEPLOY_DIR_IMAGE)**에 복사합니다.

## INCOMPATIBLE_LICENSE

빌드에서 제외해야 하는 라이센스 이름의 공백으로 구분된 목록(**[LICENSE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-LICENSE)**에 표시됨)을 지정합니다. 나열된 호환되지 않는 라이선스에 대한 대안을 제공하지 않는 레시피는 빌드되지 않습니다. 지정된 호환되지 않는 라이선스로 개별적으로 라이선스가 부여된 패키지는 삭제됩니다.
이 변수의 값에 와일드카드에 대한 일부 지원이 있지만 특정 라이선스로 제한됩니다. 현재 이러한 와일드카드만 허용되며 다음과 같이 확장됩니다.

- `AGPL-3.0*"`: `AGPL-3.0-only`, `AGPL-3.0-or-later`
- `GPL-3.0*`: `GPL-3.0-only`, `GPL-3.0-or-later`
- `LGPL-3.0*`: `LGPL-3.0-only`, `LGPL-3.0-or-later`

💡 이 기능은 다음 설정을 사용하는 경우에만 정기적으로 테스트됩니다.
`INCOMPATIBLE_LICENSE = "GPL-3.0* LGPL-3.0* AGPL-3.0*"`
다른 설정을 사용할 수 있지만 기능 시스템 이미지를 생성하는 데 필요한 구성 요소에 대한 종속성을 제거하거나 대안을 제공해야 할 수 있습니다.


## INHERIT

명명된 클래스가 전역적으로 상속되도록 합니다. 클래스 또는 클래스의 익명 기능은 기본 구성 및 각 개별 레시피에 대해 실행되지 않습니다. OpenEmbedded 빌드 시스템은 개별 레시피에서 **[INHERIT](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INHERIT)**에 대한 변경 사항을 무시합니다.
**[INHERIT](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INHERIT)**에 대한 자세한 내용은 Bitbake 사용자 매뉴얼의 “**[INHERIT Configuration Directive](https://docs.yoctoproject.org/bitbake/2.0/bitbake-user-manual/bitbake-user-manual-metadata.html#inherit-configuration-directive)**” 섹션을 참조하십시오.

## INHIBIT_PACKAGE_DEBUG_SPLIT

OpenEmbedded 빌드 시스템이 패키징하는 동안 디버그 정보를 분리하지 못하도록 합니다. 기본적으로 빌드 시스템은 do_package 작업 중에 디버깅 정보를 분할합니다. 디버그 정보 분할 방법에 대한 자세한 내용은 **[PACKAGE_DEBUG_SPLIT_STYLE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGE_DEBUG_SPLIT_STYLE)** 변수를 참조하세요.
빌드 시스템이 패키징 중에 디버그 정보를 분할하지 않도록 하려면 **[INHIBIT_PACKAGE_DEBUG_SPLIT](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INHIBIT_PACKAGE_DEBUG_SPLIT)** 변수를 다음과 같이 설정하십시오.

```bash
INHIBIT_PACKAGE_DEBUG_SPLIT = "1"
```

## INHIBIT_PACKAGE_SPLIT

"1"로 설정하면 빌드가 결과 패키지의 바이너리를 제거하지 않고 -dbg 패키지에 소스 파일이 포함되지 않도록 합니다.
기본적으로 OpenEmbedded 빌드 시스템은 바이너리를 제거하고 디버깅 기호를 `${**[PN](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PN)**}-dbg`에 넣습니다. 따라서 일반적으로 디버그할 계획인 경우 **[INHIBIT_PACKAGE_STRIP](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INHIBIT_PACKAGE_STRIP)**을 설정하지 않아야 합니다.

## INITRAMFS_FSTYPES

부팅 중에 사용되는 초기 RAM 파일 시스템(initramfs)의 출력 이미지 형식을 정의합니다. 지원되는 형식은 **[IMAGE_FSTYPES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_FSTYPES)** 변수에서 지원하는 형식과 동일합니다.
Source Directory의 `meta/conf/bitbake.conf` 설정 파일에 설정되어 있는 이 변수의 기본값은 “cpio.gz”입니다. 초기 RAM 파일 시스템 **[initrd](https://en.wikipedia.org/wiki/Initrd)** 메커니즘과 달리 Linux 커널의 initramfs 메커니즘은 선택적으로 압축된 cpio 아카이브를 기대합니다.

## INITRAMFS_IMAGE

초기 RAM 파일 시스템(initramfs) 이미지를 빌드하는 데 사용되는 이미지 레시피의 **[PROVIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PROVIDES)** 이름을 지정합니다. 즉, **[INITRAMFS_IMAGE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INITRAMFS_IMAGE)** 변수는 사용 중인 루트 파일 시스템 레시피(예: core-image-sato)에 대한 종속성으로 추가 레시피가 빌드되도록 합니다. 제공하는 initramfs 이미지 레시피는 **[IMAGE_FSTYPES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_FSTYPES)**를 **[INITRAMFS_FSTYPES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INITRAMFS_FSTYPES)**로 설정해야 합니다.
initramfs 이미지는 초기 시스템 초기화에 사용되는 임시 루트 파일 시스템을 제공합니다(예: "실제" 루트 파일 시스템을 찾고 마운트하는 데 필요한 모듈 로드).

💡 예제 initramfs 레시피는 소스 디렉토리의 meta/recipes-core/images/core-image-minimal-initramfs.bb 레시피를 참조하세요. 이 샘플 레시피를 initramfs 이미지를 제공하기 위해 빌드된 레시피로 선택하려면 **[INITRAMFS_IMAGE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INITRAMFS_IMAGE)**를 "core-image-minimal-initramfs"로 설정합니다.


**[NITRAMFS_IMAGE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INITRAMFS_IMAGE)** 변수를 사용하는 방법을 보려면 소스 디렉토리, **[image](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-image)** 클래스 및 **[kernel](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-kernel)** 클래스의 `meta-poky/conf/local.conf.sample.extended` 구성 파일을 참조하여 더 많은 정보를 찾을 수도 있습니다.
기본값인 **[INITRAMFS_IMAGE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INITRAMFS_IMAGE)**가 비어 있으면 initramfs 이미지가 빌드되지 않습니다.
자세한 내용은 생성된 이미지를 커널 이미지 내부에 묶을 수 있도록 하는 **[INITRAMFS_IMAGE_BUNDLE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-INITRAMFS_IMAGE_BUNDLE)** 변수도 확인할 수 있습니다. 추가적으로, initramfs 이미지 생성에 대한 정보는 Yocto 프로젝트 개발 작업 매뉴얼의 "**[Building an Initial RAM Filesystem (initramfs) Image](https://docs.yoctoproject.org/dev-manual/common-tasks.html#building-an-initial-ram-filesystem-initramfs-image)**" 섹션을 참조하십시오.

## KERNEL_DEVICETREE

생성된 Linux 커널 장치 트리(예: .dtb) 파일의 이름을 지정합니다.

💡 장치 트리에 대한 전체 경로를 지정하기 위한 레거시 지원이 있습니다. 그러나 .dtb 파일만 제공하는 것이 좋습니다.


이 변수를 사용하기 위해서는 **[kernel-devicetree](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-kernel-devicetree)** 클래스를 상속받아야 한다.

```bash
IMAGE_INSTALL:append = " \
    kernel-devicetree \
"
```

## LAYERDEPENDS

이 레시피가 의존하는 레이어를 공백으로 구분하여 나열합니다. 선택적으로 레이어 이름 끝에 추가하여 종속성에 대한 특정 레이어 버전을 지정할 수 있습니다. 다음은 예입니다.

```bash
LAYERDEPENDS_mylayer = "anotherlayer (=3)"
```

이 이전 예에서 "anotherlayer" 버전 3은 **[LAYERVERSION_](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-LAYERVERSION)**anotherlayer와 비교됩니다.
종속성이 누락되었거나 버전 번호(지정된 경우)가 정확히 일치하지 않으면 오류가 생성됩니다. 이 변수는 `conf/layer.conf` 파일에서 사용되며 특정 계층의 이름이 접미사로 추가되어야 합니다(예: LAYERDEPENDS_mylayer).

## LAYERRECOMMENDS

이 레이어와 함께 사용하도록 권장되는 레이어를 공백으로 구분하여 나열합니다.
선택적으로 레이어 이름 끝에 버전을 추가하여 권장 사항에 대한 특정 레이어 버전을 지정할 수 있습니다. 다음은 예입니다.

```bash
LAYERRECOMMENDS_mylayer = "anotherlayer (=3)"
```

이 이전 예에서 "anotherlayer" 버전 3은 LAYERVERSION_anotherlayer와 비교됩니다.
이 변수는 `conf/layer.conf` 파일에서 사용되며 특정 계층의 이름이 접미사로 추가되어야 합니다(예: LAYERRECOMMENDS_mylayer).

## MACHINE

이미지가 빌드되는 대상 장치를 지정합니다. 빌드 디렉토리에 있는 local.conf 파일에서 **[MACHINE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-MACHINE)**을 정의합니다. 기본적으로 MACHINE은 QEMU를 사용하여 에뮬레이트되는 x86 기반 아키텍처 시스템인 "qemux86"으로 설정됩니다.

```bash
MACHINE ?= "qemux86"
```

변수는 동일한 이름의 시스템 구성 파일에 해당하며 이를 통해 시스템별 구성이 설정됩니다. 따라서 **[MACHINE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-MACHINE)**이 "qemux86"으로 설정되면 해당 `qemux86.conf` 시스템 구성 파일은 `meta/conf/machine`의 소스 디렉토리에서 찾을 수 있습니다.
출하 시 Yocto 프로젝트에서 지원하는 기계 목록은 다음과 같습니다.

```bash
MACHINE ?= "qemuarm"
MACHINE ?= "qemuarm64"
MACHINE ?= "qemumips"
MACHINE ?= "qemumips64"
MACHINE ?= "qemuppc"
MACHINE ?= "qemux86"
MACHINE ?= "qemux86-64"
MACHINE ?= "genericx86"
MACHINE ?= "genericx86-64"
MACHINE ?= "beaglebone"
MACHINE ?= "edgerouter"
```

마지막 5개는 meta-yocto-bsp 계층에서 제공되는 Yocto 프로젝트 참조 하드웨어 보드입니다.

💡 구성에 추가 BSP(보드 지원 패키지) 레이어를 추가하면 MACHINE에 대해 가능한 새로운 설정이 추가됩니다.


## NO_RECOMMENDATIONS

모든 "recommended-only" 패키지의 설치를 방지합니다. 권장 전용 패키지는 **[RRECOMMENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RRECOMMENDS)** 변수를 통해서만 설치되는 패키지입니다. **[NO_RECOMMENDATIONS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-NO_RECOMMENDATIONS)** 변수를 "1"로 설정하면 이 기능이 켜집니다.

```bash
NO_RECOMMENDATIONS = "1"
```

이 변수를 local.conf 파일에서 전역적으로 설정하거나 레시피 이름 재정의를 사용하여 특정 이미지 레시피에 첨부할 수 있습니다.

```bash
NO_RECOMMENDATIONS:pn-target_image = "1"
```

이 변수를 사용하여 패키지를 설치하지 않기로 선택하고 일부 다른 패키지가 종속되어 있는 경우(예: 레시피의 **[RDEPENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RDEPENDS)** 변수에 나열됨) OpenEmbedded 빌드 시스템은 요청을 무시하고 종속성 오류를 피하기 위해 패키지를 설치합니다. .

💡 커널 모듈과 같은 특정 시스템 기능에는 일부 권장 패키지가 필요할 수 있습니다. **[IMAGE_INSTALL](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_INSTALL)** 변수를 사용하여 패키지를 추가하는 것은 사용자의 몫입니다.


이 변수는 IPK 및 RPM 패키징 백엔드를 사용할 때만 지원됩니다. DEB는 지원되지 않습니다.
관련 정보는 **[BAD_RECOMMENDATIONS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-BAD_RECOMMENDATIONS)** 및 **[PACKAGE_EXCLUDE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGE_EXCLUDE)** 변수를 참조하십시오.

## OEROOT

최상위 빌드 환경 설정 스크립트가 제공되는 디렉토리입니다. Yocto 프로젝트는 최상위 빌드 환경 설정 스크립트인 **[oe-init-build-env](https://docs.yoctoproject.org/ref-manual/structure.html#structure-core-script)**를 제공합니다. 이 스크립트를 실행하면 **[OEROOT](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-OEROOT)** 변수가 스크립트가 포함된 디렉토리로 확인됩니다.
이 변수가 사용되는 방법에 대한 추가 정보는 초기화 스크립트를 참조하십시오.

## OVERRIDES

현재 적용되는 재정의의 콜론으로 구분된 목록입니다. 재정의는 구문 분석이 끝날 때 변수를 선택적으로 재정의할 수 있는 BitBake 메커니즘입니다. **[OVERRIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-OVERRIDES)**의 재정의 세트는 현재 빌드 중인 레시피, 빌드 중인 기계 등을 포함하는 빌드 중 "상태"를 나타냅니다.
예를 들어, 문자열 "an-override"가 **[OVERRIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-OVERRIDES)**의 콜론으로 구분된 목록의 요소로 나타나는 경우 다음 할당은 구문 분석이 끝날 때 "overridden" 값으로 FOO를 재정의합니다.

```bash
FOO:an-override = "overridden"
```

재정의 메커니즘에 대한 자세한 정보는 BitBake 사용자 매뉴얼의 "**[Conditional Syntax (Overrides)](https://docs.yoctoproject.org/bitbake/2.0/bitbake-user-manual/bitbake-user-manual-metadata.html#conditional-syntax-overrides)**" 섹션을 참조하십시오.
**[OVERRIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-OVERRIDES)**의 기본값에는 **[CLASSOVERRIDE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-CLASSOVERRIDE)**, **[MACHINEOVERRIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-MACHINEOVERRIDES)** 및 **[DISTROOVERRIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTROOVERRIDES)** 변수 값이 포함됩니다. 기본적으로 포함된 또 다른 중요한 재정의는 pn-${PN}입니다. 이 재정의를 통해 구성(.conf) 파일 내의 단일 레시피에 대해 변수를 설정할 수 있습니다. 다음은 예입니다.

```bash
FOO:pn-myrecipe = "myrecipe-specific value"
```

💡 어떤 재정의가 적용되는지 확인하는 쉬운 방법은 bitbake -e 명령의 출력에서 **[OVERRIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-OVERRIDES)**를 검색하는 것입니다. 자세한 내용은 Yocto 프로젝트 개발 작업 매뉴얼의 "**[Viewing Variable Values](https://docs.yoctoproject.org/dev-manual/common-tasks.html#viewing-variable-values)**" 섹션을 참조하십시오.


## P

레시피 이름 및 버전. P는 다음으로 구성됩니다.

```bash
${PN}-${PV}
```

## PACKAGECONFIG

이 변수는 레시피별로 레시피 기능을 활성화 또는 비활성화하는 수단을 제공합니다. [**PACKAGECONFIG**](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG) 블록은 기능을 지정한 다음 기능 동작을 정의하는 인수를 지정할 때 레시피에 정의됩니다. 다음은 기본 블록 구조입니다(가독성을 위해 여러 줄로 구분).

```bash
PACKAGECONFIG ??= "f1 f2 f3 ..."
PACKAGECONFIG[f1] = "\
    --with-f1, \
    --without-f1, \
    build-deps-for-f1, \
    runtime-deps-for-f1, \
    runtime-recommends-for-f1, \
    packageconfig-conflicts-for-f1"
PACKAGECONFIG[f2] = "\
     ... and so on and so on ...
```

**[PACKAGECONFIG](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG)** 변수 자체는 활성화할 기능의 공백으로 구분된 목록을 지정합니다. 기능 다음에 쉼표로 구분된 최대 6개의 순서 종속 인수를 제공하여 각 기능의 동작을 결정할 수 있습니다. 원하는 인수를 생략할 수 있지만 구분 쉼표는 유지해야 합니다. 순서는 중요하며 다음을 지정합니다.

1. 기능이 활성화된 경우 구성 스크립트 인수 목록(**[EXTRA_OECONF](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-EXTRA_OECONF)** 또는 **[PACKAGECONFIG_CONFARGS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG_CONFARGS)**)에 추가해야 하는 추가 인수입니다.
2. 기능이 비활성화된 경우 **[EXTRA_OECONF](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-EXTRA_OECONF)** 또는 **[PACKAGECONFIG_CONFARGS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG_CONFARGS)**에 추가해야 하는 추가 인수입니다.
3. 기능이 활성화된 경우 추가해야 하는 추가 빌드 종속성(**[DEPENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DEPENDS)**)입니다.
4. 기능이 활성화된 경우 추가해야 하는 추가 런타임 종속성(**[RDEPENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RDEPENDS)**)입니다.
5. 기능이 활성화된 경우 추가해야 하는 추가 런타임 권장 사항(**[RRECOMMENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RRECOMMENDS)**)입니다.
6. 이 기능에 대해 충돌하는(즉, 상호 배타적인) **[PACKAGECONFIG](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG)** 설정입니다.

librsvg 레시피에서 가져온 다음 **[PACKAGECONFIG](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG)** 블록을 고려하십시오. 이 예에서 기능은 기능의 동작을 결정하는 세 가지 인수가 있는 gtk입니다.

```bash
PACKAGECONFIG[gtk] = "--with-gtk3,--without-gtk3,gtk+3"
```

-with-gtk3 및 gtk+3 인수는 기능이 활성화된 경우에만 적용됩니다. 이 경우 구성 스크립트 인수 목록에 --with-gtk3이 추가되고 **[DEPENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DEPENDS)**에 gtk+3이 추가됩니다. 반면에 다른 계층의 .bbappend 파일을 통해 기능이 비활성화된 경우 대신 구성 스크립트에 두 번째 인수 --without-gtk3가 추가됩니다.
앞에서 설명한 기본 **[PACKAGECONFIG](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG)** 구조는 블록을 생성하든 변경하든 상관없이 적용됩니다. 블록을 생성할 때 레시피 내부의 구조를 사용하십시오.
기존 **[PACKAGECONFIG](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGECONFIG)** 블록을 변경하려면 다음 두 가지 방법 중 하나를 수행할 수 있습니다.

- **파일 추가**: 레이어에 recipename.bbappend라는 추가 파일을 만들고 PACKAGECONFIG 값을 재정의합니다. 변수를 완전히 재정의할 수 있습니다.

```bash
PACKAGECONFIG = "f4 f5"
```

또는 변수를 추가할 수 있습니다.

```bash
PACKAGECONFIG:append = " f4"
```

- **구성 파일**: 이 방법은 local.conf 또는 mydistro.conf 파일을 편집하는 것을 제외하고 추가 파일을 통해 블록을 변경하는 것과 동일합니다. 앞에서 설명한 추가 파일과 마찬가지로 변수를 완전히 재정의할 수 있습니다.

```bash
PACKAGECONFIG:pn-recipename = "f4 f5"
```

또는 변수를 추가할 수 있습니다.

```bash
PACKAGECONFIG:append:pn-recipename = " f4"
```

## PN

이 변수는 컨텍스트에 따라 두 개의 개별 기능을 가질 수 있습니다: 레시피 이름 또는 결과 패키지 이름.
PN은 OpenEmbedded 빌드 시스템이 패키지를 생성하기 위한 입력으로 사용하는 파일 컨텍스트의 레시피 이름을 나타냅니다. 이름은 일반적으로 레시피 파일 이름에서 추출됩니다. 예를 들어, 레시피 이름이 expat_2.0.1.bb인 경우 PN의 기본값은 "expat"입니다.
변수는 OpenEmbedded 빌드 시스템에 의해 생성되거나 생성된 파일의 컨텍스트에서 패키지 이름을 참조합니다.
해당되는 경우 PN 변수에는 특수 접미사 또는 접두사도 포함됩니다. 예를 들어 bash를 사용하여 기본 시스템용 패키지를 빌드하는 경우 PN은 bash 기본입니다. bash를 사용하여 대상 및 Multilib용 패키지를 빌드하는 경우 PN은 각각 bash 및 lib64-bash가 됩니다.

## PR

레시피의 개정판. 이 변수의 기본값은 "r0"입니다. 레시피의 후속 수정은 일반적으로 "r1", "r2" 등의 값을 갖습니다. **[PV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PV)**가 증가하면 **[PR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PR)**은 일반적으로 "r0"으로 재설정됩니다.

💡 OpenEmbedded 빌드 시스템은 언제 레시피를 다시 빌드해야 하는지 알기 위해 **[PR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PR)**의 도움이 필요하지 않습니다. 빌드 시스템은 **[stamp](https://docs.yoctoproject.org/ref-manual/structure.html#structure-build-tmp-stamps)** 및 **[Shared State Cache](https://docs.yoctoproject.org/overview-manual/concepts.html#shared-state-cache)** 메커니즘과 함께 작업 **[input checksums](https://docs.yoctoproject.org/overview-manual/concepts.html#checksums-signatures)**을 사용합니다.


PR 변수는 패키지 관리자가 이미 빌드된 이미지에 패키지를 동적으로 설치할 때 주로 중요합니다. 이 경우 PKGR의 기본값인 PR은 많은 패키지가 동일한 PV(즉, PKGV)를 갖는 경우 패키지 관리자가 어떤 패키지가 가장 최근 패키지인지 구별하는 데 도움이 됩니다. 동일한 PV를 가진 많은 패키지가 있는 구성 요소는 일반적으로 패키지가 모두 동일한 업스트림 버전을 설치하지만 패키징 수정 사항을 포함하는 이후(PR) 버전 패키지를 설치한다는 것을 의미합니다.

## PREFERRED_PROVIDER

여러 레시피가 동일한 항목을 제공하는 경우 이 변수는 선호하는 레시피를 결정하여 항목(즉, 기본 제공자)을 제공합니다. 항상 이 변수에 제공된 항목의 이름을 접미사로 붙여야 합니다. 그리고, 선호하는 레시피의 이름(**[PN](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PN)**)을 사용하여 변수를 정의해야 합니다. 다음은 일반적인 예입니다.

```bash
PREFERRED_PROVIDER_virtual/kernel ?= "linux-yocto"
```

이전 예에서 여러 레시피가 "virtual/kernel"을 제공하고 있습니다. **[PREFERRED_PROVIDER](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PREFERRED_PROVIDER)** 변수는 "virtual/kernel"을 제공하려는 레시피의 이름(**[PN](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PN)**)으로 설정됩니다.
다음은 더 많은 예입니다.

```bash
PREFERRED_PROVIDER_virtual/xserver = "xserver-xf86"
PREFERRED_PROVIDER_virtual/libgl ?= "mesa"
```

자세한 내용은 Yocto 프로젝트 개발 작업 매뉴얼의 “**[Using Virtual Providers](https://docs.yoctoproject.org/dev-manual/common-tasks.html#using-virtual-providers)**” 섹션을 참조하십시오.

💡 가상/\* 항목을 **[PREFERRED_PROVIDER](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PREFERRED_PROVIDER)**와 함께 사용하는 경우 해당 항목을 제공하지만 **[PREFERRED_PROVIDER](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PREFERRED_PROVIDER)**에 의해 선택(정의)되지 않은 모든 레시피가 빌드되지 않습니다. 이는 이 메커니즘이 상호 배타적인 대체 제공자 사이에서 선택하도록 설계되었기 때문에 일반적으로 바람직합니다.


## PROVIDES

특정 레시피를 알 수 있는 별칭 목록입니다. 기본적으로 레시피의 자체 PN은 암시적으로 이미 PROVIDES 목록에 있으므로 자체적으로 제공한다고 언급할 필요가 없습니다. 레시피가 PROVIDES를 사용하는 경우 추가 별칭은 레시피의 동의어이며 DEPENDS에 지정된 대로 빌드 중에 다른 레시피의 종속성을 충족하는 데 유용할 수 있습니다.
레시피 파일 eudev_3.2.9.bb에서 다음 예제 PROVIDES 문을 고려하십시오.

```bash
PROVIDES += "udev"
```

PROVIDES 문은 "eudev" 레시피를 단순히 "udev"로도 사용할 수 있게 합니다.

💡 레시피 고유의 레시피 이름(PN)은 항상 암시적으로 PROVIDES 앞에 추가되므로 위의 예에서 "+="를 사용할 필요는 없지만 혼동을 피하기 위해 권장됩니다.


대체 이름으로 레시피를 제공하는 것 외에도 **[PROVIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PROVIDES)** 메커니즘을 사용하여 가상 대상을 구현합니다. 가상 대상은 특정 기능(예: Linux 커널)에 해당하는 이름입니다. 문제의 기능을 제공하는 레시피는 **[PROVIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PROVIDES)**의 가상 대상을 나열합니다. 문제의 기능에 의존하는 레시피는 **[DEPENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DEPENDS)**에 가상 타겟을 포함하여 제공자의 선택을 열어둘 수 있습니다.
일반적으로 가상 대상은 "가상/기능"(예: “virtual/kernel") 형식의 이름을 갖습니다. 슬래시는 단순히 이름의 일부이며 구문상 의미가 없습니다.
**[PREFERRED_PROVIDER](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PREFERRED_PROVIDER)** 변수는 가상 대상을 제공하는 특정 레시피를 선택하는 데 사용됩니다.

💡 가상 런타임 종속성(패키지)에 대한 해당 메커니즘이 존재합니다. 그러나 메커니즘은 일반 변수 할당 이외의 특수 기능에 의존하지 않습니다. 예를 들어 VIRTUAL-RUNTIME_dev_manager는 /dev 디렉토리를 관리하는 구성 요소의 패키지를 나타냅니다.
런타임 종속성에 대한 "선호 공급자"를 설정하는 것은 구성 파일에서 다음 할당을 사용하는 것만큼 간단합니다.
`VIRTUAL-RUNTIME_dev_manager = "udev"`


## PV

[PV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PV) is the default value of the [PKGV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PKGV) variable.

레시피의 버전입니다. 버전은 일반적으로 레시피 파일 이름에서 추출됩니다. 예를 들어, 레시피 이름이 `expat_2.0.1.bb`인 경우 **[PV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PV)**의 기본값은 "2.0.1"입니다. **[PV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PV)**는 소스 코드 저장소(예: Git 또는 Subversion)에서 불안정한(예: 개발) 버전을 빌드하지 않는 한 일반적으로 레시피 내에서 재정의되지 않습니다.
**[PV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PV)**는 **[PKGV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PKGV)** 변수의 기본값입니다.

## RDEPENDS

패키지의 런타임 종속성을 나열합니다. 이러한 종속성은 패키지가 올바르게 작동하기 위해 설치해야 하는 다른 패키지입니다. 예를 들어 다음 할당은 패키지 foo에 패키지 bar 및 baz를 설치해야 한다고 선언합니다.

```bash
RDEPENDS:foo = "bar baz"
```

```bash
RDEPENDS:${PN}-dev += "perl"
```

## ROOTFS

루트 파일 시스템으로 포함할 파일 시스템 이미지를 나타냅니다.
**[ROOTFS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-ROOTFS)** 변수는 **[image-live](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-image-live)** 클래스와 함께 사용되는 선택적 변수입니다.

## ROOTFS_POSTINSTALL_COMMAND

OpenEmbedded 빌드 시스템이 패키지를 설치한 후 호출할 함수 목록을 지정합니다. 세미콜론으로 구분된 함수를 지정할 수 있습니다.

```bash
ROOTFS_POSTINSTALL_COMMAND += "function; ... "
```

함수 내의 명령에 루트 파일 시스템 경로를 전달해야 하는 경우 루트 파일 시스템 이미지가 되는 디렉토리를 가리키는 `${IMAGE_ROOTFS}`를 사용할 수 있습니다. 자세한 내용은 **[IMAGE_ROOTFS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_ROOTFS)** 변수를 참조하십시오.

## ROOTFS_POSTPROCESS_COMMAND

OpenEmbedded 빌드 시스템이 루트 파일 시스템을 만든 후 호출할 함수 목록을 지정합니다. 세미콜론으로 구분된 함수를 지정할 수 있습니다.

```bash
ROOTFS_POSTPROCESS_COMMAND += "function; ... "
```

함수 내의 명령에 루트 파일 시스템 경로를 전달해야 하는 경우 루트 파일 시스템 이미지가 되는 디렉토리를 가리키는 `${IMAGE_ROOTFS}`를 사용할 수 있습니다. 자세한 내용은 **[IMAGE_ROOTFS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_ROOTFS)** 변수를 참조하십시오.

## ROOTFS_POSTUNINSTALL_COMMAND

OpenEmbedded 빌드 시스템이 불필요한 패키지를 제거한 후 호출할 함수 목록을 지정합니다. 이미지에서 런타임 패키지 관리가 비활성화되면 base-passwd, shadow 및 update-alternatives를 포함한 여러 패키지가 제거됩니다. 세미콜론으로 구분된 함수를 지정할 수 있습니다.

```bash
ROOTFS_POSTUNINSTALL_COMMAND += "function; ... "
```

함수 내의 명령에 루트 파일 시스템 경로를 전달해야 하는 경우 루트 파일 시스템 이미지가 되는 디렉토리를 가리키는 `${IMAGE_ROOTFS}`를 사용할 수 있습니다. 자세한 내용은 **[IMAGE_ROOTFS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_ROOTFS)** 변수를 참조하십시오.

## ROOTFS_PREPROCESS_COMMAND

OpenEmbedded 빌드 시스템이 루트 파일 시스템을 생성하기 전에 호출할 함수 목록을 지정합니다. 세미콜론으로 구분된 함수를 지정할 수 있습니다.

```bash
ROOTFS_PREPROCESS_COMMAND += "function; ... "
```

함수 내의 명령에 루트 파일 시스템 경로를 전달해야 하는 경우 루트 파일 시스템 이미지가 되는 디렉토리를 가리키는 `${IMAGE_ROOTFS}`를 사용할 수 있습니다. 자세한 내용은 **[IMAGE_ROOTFS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_ROOTFS)** 변수를 참조하십시오.

## RRECOMMENDS

빌드 중인 패키지의 사용성을 확장하는 패키지 목록입니다. 빌드 중인 패키지는 성공적으로 빌드하기 위해 이 패키지 목록에 의존하지 않고 확장된 사용성을 위해 사용합니다. 패키지에 대한 런타임 종속성을 지정하려면 **[RDEPENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RDEPENDS)** 변수를 참조하십시오.
패키지 관리자는 빌드된 패키지를 설치할 때 **[RRECOMMENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RRECOMMENDS)** 패키지 목록을 자동으로 설치합니다. 그러나 **[BAD_RECOMMENDATIONS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-BAD_RECOMMENDATIONS)**, **[NO_RECOMMENDATIONS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-NO_RECOMMENDATIONS)** 및 **[PACKAGE_EXCLUDE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGE_EXCLUDE)** 변수를 사용하여 나열된 패키지가 설치되는 것을 방지할 수 있습니다.
**[RRECOMMENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RRECOMMENDS)**에 지정된 패키지는 실제로 생성할 필요가 없습니다. 그러나 **[PACKAGES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGES)** 또는 **[PACKAGES_DYNAMIC](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGES_DYNAMIC)** 변수 또는 **[RPROVIDES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RPROVIDES)** 변수를 통해 각 패키지를 제공하는 레시피가 있어야 하며 그렇지 않으면 빌드 중에 오류가 발생합니다. 이러한 레시피가 존재하고 패키지가 생성되지 않으면 빌드가 오류 없이 계속됩니다.
**[RRECOMMENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RRECOMMENDS)** 변수는 빌드 중인 패키지에 적용되기 때문에 항상 변수에 재정의를 첨부하여 유용성이 확장되는 특정 패키지를 지정해야 합니다. 예를 들어, 무선 기능을 지원하도록 확장된 개발 패키지를 구축한다고 가정합니다. 이 경우 다음을 사용합니다.

```bash
RRECOMMENDS:${PN}-dev += "wireless_package_name"
```

예에서 패키지 이름(${PN}-dev)은 **[debian.bbclass](https://docs.yoctoproject.org/ref-manual/classes.html#ref-classes-debian)**와 같은 클래스에 의해 출력 패키지의 이름을 바꾸기 전에 **[PACKAGES](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PACKAGES)** 네임스페이스에서와 같이 나타나야 합니다.
OpenEmbedded 빌드 시스템이 사용하는 BitBake는 버전이 지정된 권장 사항 지정을 지원합니다. 구문은 패키징 형식에 따라 다르지만 BitBake는 이러한 차이점을 숨깁니다. 다음은 **[RRECOMMENDS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-RRECOMMENDS)** 변수를 사용하여 버전을 지정하는 일반 구문입니다.

```bash
RRECOMMENDS:${PN} = "package (operator version)"
```

연산자에 대해 다음을 지정할 수 있습니다.

- =
- <
- >
- <=
- >=

예를 들어 다음은 foo 패키지 버전 1.2 이상에 대한 권장 사항을 설정합니다.

```bash
RRECOMMENDS:${PN} = "foo (>= 1.2)"
```

## S

압축을 푼 레시피 소스 코드가 있는 **[Build Directory](https://docs.yoctoproject.org/ref-manual/terms.html#term-Build-Directory)**의 위치입니다. 기본적으로 이 디렉토리는 ${**[WORKDIR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-WORKDIR)**}/${**[BPN](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-BPN)**}-${**[PV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PV)**}입니다. 여기서 `${BPN}`은 기본 레시피 이름이고 `${PV}`는 레시피 버전입니다. 소스 tarball이 `${BPN}-${PV}` 이외의 다른 이름의 디렉토리로 코드를 추출하거나 소스 코드가 Git 또는 Subversion과 같은 SCM에서 가져온 경우 레시피에서 S를 설정해야 다음과 같이 됩니다. OpenEmbedded 빌드 시스템은 압축을 푼 소스를 찾을 위치를 알고 있습니다.
예를 들어 poky라는 이름의 소스 디렉토리 최상위 폴더와 poky/build에 기본 빌드 디렉토리가 있다고 가정합니다. 이 경우 빌드 시스템이 db에 대한 압축을 푼 레시피를 유지하는 데 사용하는 작업 디렉터리는 다음과 같습니다.

```bash
poky/build/tmp/work/qemux86-poky-linux/db/5.1.19-r3/db-5.1.19
```

압축을 푼 소스 코드는 db-5.1.19 폴더에 있습니다.
다음 예에서는 Git 리포지토리를 가정합니다. 기본적으로 Git 리포지토리는 do_fetch 동안 ${WORKDIR}/git에 복제됩니다. 이 경로는 기본값 S와 다르기 때문에 소스를 찾을 수 있도록 구체적으로 설정해야 합니다.

```bash
SRC_URI = "git://path/to/repo.git;branch=main"
S = "${WORKDIR}/git"
```

## **SDK_NAME**

SDK 출력 파일의 기본 이름입니다. 이름은 **[DISTRO](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-DISTRO)**, **[TCLIBC](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-TCLIBC)**, **[SDK_ARCH](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SDK_ARCH)**, **[IMAGE_BASENAME](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-IMAGE_BASENAME)** 및 **[TUNE_PKGARCH](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-TUNE_PKGARCH)** 변수에서 파생됩니다.

```bash
SDK_NAME = "${DISTRO}-${TCLIBC}-${SDK_ARCH}-${IMAGE_BASENAME}-${TUNE_PKGARCH}"
```

## SRC_URI

이 변수 **[SRC_URI](https://docs.yoctoproject.org/bitbake/2.0/bitbake-user-manual/bitbake-user-manual-ref-variables.html#term-SRC_URI)**에 대한 초기 설명은 BitBake 매뉴얼을 참조하십시오.
다음 기능은 OpenEmbedded 및 Yocto 프로젝트에 의해 추가되었습니다.
표준 및 레시피별 옵션이 있습니다. 다음은 표준입니다.

- `apply` - 패치 적용 여부. 기본 작업은 패치를 적용하는 것입니다.
- `striplevel` - 패치를 적용할 때 사용할 스트립 레벨입니다. 기본 수준은 1입니다.
- `patchdir` - 패치를 적용해야 하는 디렉토리를 지정합니다. 기본값은 `${S}`입니다.

다음은 개정 관리 시스템에서 코드를 빌드하는 레시피에 특정한 옵션입니다.

- `mindate` - Apply the patch only if [SRCDATE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCDATE) is equal to or greater than `mindate`.
- `maxdate` - Apply the patch only if [SRCDATE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCDATE) is not later than `maxdate`.
- `minrev` - Apply the patch only if [SRCREV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCREV) is equal to or greater than `minrev`.
- `maxrev` - Apply the patch only if [SRCREV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCREV) is not later than `maxrev`.
- `rev` - Apply the patch only if [SRCREV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCREV) is equal to `rev`.
- `notrev` - Apply the patch only if [SRCREV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCREV) is not equal to `rev`.

💡 빌드 시스템이 추가 파일에서 **[SRC_URI](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRC_URI)** 문을 통해 지정된 파일을 선택하도록 하려면 추가 파일 내에서 **[FILESEXTRAPATHS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-FILESEXTRAPATHS)**  변수도 사용하여 **[FILESPATH](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-FILESPATH)** 변수를 확장해야 합니다.


## SRCREV

패키지를 빌드하는 데 사용된 소스 코드의 개정판입니다. 이 변수는 Subversion, Git, Mercurial 및 Bazaar에만 적용됩니다. 고정 버전을 만들고 BitBake가 레시피를 구문 분석할 때마다 원격 저장소에서 쿼리를 수행하지 않으려면 태그가 아닌 전체 버전 식별자인 **[SRCREV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCREV)**를 지정해야 합니다.

💡 **[SRCREV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-SRCREV)**를 사용하여 최신 소프트웨어 개정판을 상속할 때의 제한 사항에 대한 정보는 **[AUTOREV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-AUTOREV)** 변수 설명과 Yocto 프로젝트 개발 작업 매뉴얼에 있는 “[Automatically Incrementing a Package Version Number](https://docs.yoctoproject.org/dev-manual/common-tasks.html#automatically-incrementing-a-package-version-number)” 섹션을 참조하십시오.


## TCLIBC

빌드 프로세스 중에 사용할 GNU 표준 C 라이브러리(libc) 변형을 지정합니다.
"glibc", "musl", "newlib" 또는 "baremetal"을 선택할 수 있습니다.

## WKS_FILE

파티션된 이미지(`image.wic`)를 생성하기 위해 OpenEmbedded 빌드 시스템에서 사용하는 Wic 킥스타트 파일의 위치를 지정합니다. 분할된 이미지를 만드는 방법에 대한 정보는 Yocto 프로젝트 개발 작업 매뉴얼의 “[Creating Partitioned Images Using Wic](https://docs.yoctoproject.org/dev-manual/common-tasks.html#creating-partitioned-images-using-wic)” 섹션을 참조하십시오. 킥스타트 파일 형식에 대한 자세한 내용은 “[OpenEmbedded Kickstart (.wks) Reference](https://docs.yoctoproject.org/ref-manual/kickstart.html)” 장을 참조하십시오.

## WORKDIR

OpenEmbedded 빌드 시스템이 레시피를 빌드하는 작업 디렉터리의 경로 이름입니다. 이 디렉토리는 **[TMPDIR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-TMPDIR)** 디렉토리 구조 내에 있으며 빌드 중인 레시피와 빌드되는 시스템에 따라 다릅니다.
**[WORKDIR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-WORKDIR)** 디렉토리는 다음과 같이 정의됩니다.

```bash
${TMPDIR}/work/${MULTIMACH_TARGET_SYS}/${PN}/${EXTENDPE}${PV}-${PR}
```

실제 디렉토리는 다음과 같은 몇 가지 사항에 따라 달라집니다.

- **[TMPDIR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-TMPDIR)**: 최상위 빌드 출력 디렉토리
- **[MULTIMACH_TARGET_SYS](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-MULTIMACH_TARGET_SYS)**: 대상 시스템 식별자
- **[PN](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PN)**: 레시피 이름
- **[EXTENDPE](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-EXTENDPE)**: The epoch - (대부분의 레시피의 경우인 PE가 지정되지 않은 경우 EXTENDPE는 비어 있음)
- **[PV](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PV)**: 레시피 버전
- **[PR](https://docs.yoctoproject.org/ref-manual/variables.html?highlight=rrecommends#term-PR)**: 레시피 개정판

예를 들어 소스 디렉토리 최상위 폴더 이름 poky, 기본 빌드 디렉토리(poky/build) 및 qemux86-poky-linux 시스템 대상 시스템을 가정합니다. 또한 레시피의 이름이 foo_1.3.0-r0.bb라고 가정합니다. 이 경우 빌드 시스템이 패키지를 빌드하는 데 사용하는 작업 디렉토리는 다음과 같습니다.

```bash
poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0
```%    