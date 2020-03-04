---
layout: post
title: CentOS RPM Dependency 패키지 수동 설치 (yum 사용 불가시)
category: OS
---

CentOS에서 RPM 파일로 수동 설치해야 하는 경우가 생기는데 종속성 때문에 문제가 생긴다.
이를 해결하기 위한 방편에 대해 포스팅한다. 
~~yum-utils를 사용해 yum 사용이 가능한 서버에서 해당 패키지를 받아 rpm 파일들을 옮겨 설치하는 방법을 사용한다.~~

> 위 방법은 의존성에 걸린 모든 파일이 아니라 현재 시스템에 필요한 파일들만 다운로드 한다.
 yum의 Downloadonly 플러그인으로 해결하는게 낫다.

## ~~1 yum utils 설치~~  

~~~bash
yum install yum-utils -y
~~~

## 1. downloadonly 패키지 설치

~~~bash
yum install yum-plugin-downloadonly -y
~~~

## ~~2 종속성 관련된 RPM 파일 다운로드 (yum util 사용)~~

아래 명령어를 수행하면 $DOWNLOAD_DIR에 $PACKAGE가 의존성이 있는 rpm 파일들이 다운로드 된다.

```bash
yumdownload --resolve --destdir=$DOWNLOAD_DIR $PACKAGE
```

## 2 downloadonly 패키지 이용하여 다운로드

downloadonly 패키지 또한 yum util과 마찬가지로 현 시스템에 필요한 의존 패키지마 설치하는데 이걸 방지 하기 위해서 --installroot 옵션을 넣는다. 실제 설치가 필요한게 아니라 rpm 파일들을 받기위한 옵션이므로 아무 디렉토리나 지정하고 나중에 지워도 된다. --releasever 옵션은 centos7에서 발생하는 오류를 해결하기 위한 옵션이다.

```bash
yum install \
  --installroot=$INSTALL_DIR \
  --downloadonly --downloaddir=$DOWNLOAD_DIR $PACKAGE \
  --releasever=/
```

## 3. RPM 설치할 서버로 옮겨 설치

위에서 다운 받은 rpm 파일들을 설치 하고자하는 서버로 옮겨 설치한다.

```bash
rpm -Uvh *.rpm
```

