---
layout: post
title: CentOS RPM Dependency 패키지 수동 설치 (yum 사용 불가시)
category: OS
---

CentOS에서 RPM 파일로 수동 설치해야 하는 경우가 생기는데 종속성 때문에 문제가 생긴다.
이를 해결하기 위한 방편에 대해 포스팅한다. 
yum-utils를 사용해 yum 사용이 가능한 서버에서 해당 패키지를 받아 rpm 파일들을 옮겨 설치하는 방법을 사용한다.

## 1. yum utils 설치

```bash
# yum install yum utils -y
```

## 2. 종속성 관련된 RPM 파일 다운로드

아래 명령어를 수행하면 $DOWNLOAD_DIR에 $PACKAGE가 의존성이 있는 rpm 파일들이 다운로드 된다.

```bash
# yumdownload --resolve --destdir=$DOWNLOAD_DIR $PACKAGE
```

## 3. RPM 설치할 서버로 옮겨 설치

위에서 다운 받은 rpm 파일들을 설치 하고자하는 서버로 옮겨 설치한다.

```bash
$ rpm -Uvh *.rpm
```