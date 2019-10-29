---
layout: post
title: SMS 포워딩 하기 (안드로이드)
category: Etc
---
SMS 포워딩 하기 위한 앱들이 많은데 그 중에서 Automate라는 앱을 이용해서 SMS 포워딩이 가능하다. 이 글은 Automate라는 앱으로 특정 번호로 내 휴대폰에 온 SMS를 다른 휴대폰 번호로 그대로 포워딩 기능을 순서도로 만들어 보는 방법을 포스팅한 글이다.

## 1. automate app 설치

다음 링크에서 automate 앱을 설치 한다. [automate 설치 url](https://play.google.com/store/apps/details?id=com.llamalab.automate&hl=ko)

## 2. flowchart 생성

![automate 초기 실행 화면](/img/2019-10-29-SMS-Forwarding/1.jpg){:width="35%"}

우 상단 "+"를 눌러 Flow Chart를 생성한다. 

![FlowChart 생성 후](/img/2019-10-29-SMS-Forwarding/2.jpg){:width="35%"}

Flow Chart를 생성하면 위와 같은 화면이 나타난다.

위 사진의 Flow Beginning 블록이 순서도의 시작이라고 보면 된다.

이제 순서도 블록을 추가해 보도록 한다.

## 3. flowchart 블록 추가

위 그림에서 좌 상단의 공구 아이콘을 클릭한다. 

![공구 아이콘 클릭](/img/2019-10-29-SMS-Forwarding/3.jpg){:width="35%"}

공구 아이콘을 클릭하면 위 화면과 같이 여러 기능에 대한 블록을 선택할 수 있다.

이 중 MESSAGING 카테고리를 누르면 아래와 같은 화면이 나타난다. 

![MESSAGING 카테고리 클릭](/img/2019-10-29-SMS-Forwarding/4.jpg){:width="35%"}

위 화면 나타난 것 처럼 E-mail을 보내는 블록(E-mail send), Gmail을 보내는 블록(Gmail Send), SMS를 받았을 때 처리할 블록(SMS received), SMS를 보내는 블록(Send SMS) 등등의 블록을 선택할 수 있는데 이 글에서는 SMS 포워딩을 할 것이므로 "SMS received" 블록과 "Send SMS" 블록을 이용할 것이다.

### 3-1. SMS 수신 블록 추가

먼저 SMS를 수신 받는 이벤트를 처리해야하므로 "SMS received"를 클릭해 해당 블록을 추가한다. 

![SMS 수신 블록 추가](/img/2019-10-29-SMS-Forwarding/5.jpg){:width="35%"}

위 화면은 SMS 수신 블록을 추가하면 나타나는 화면이다. 

![블록 연결](/img/2019-10-29-SMS-Forwarding/6.jpg){:width="35%"}

블록 추가 후에 위처럼 시작 블록인 "Flow beginning" 블록과 "SMS received" 블록을 연결할 수 있다. 이처럼 블록들을 연결하여 순서대로 실행할 수 있다. 
이제 SMS received 블록이 수행할 내용에 대해 설정해보자. SMS recevied 블록을 클릭한다. 

![SMS 수신 블록 설정](/img/2019-10-29-SMS-Forwarding/7.jpg){:width="35%"}

위 화면에 나타난 PHONE NUMBER 항목에 내가 수신을 감지할 번호를 넣어 준다. 

> 예) 0311231234번호로부터 온 문자들에 대해 감지하고 싶으면 0311231234를 PHONE NUMBER에 넣어준다.

Message 항목에는 SMS내용이 담길 식별자를 넣어준다. 프로그래밍에서 변수를 선언하고 이에 SMS 내용을 담는다고 생각하면 된다.

> 예) Message 항목에 smsMsg라고 넣어주면 현재 Flow Chart의 다른 블록에서 해당 식별자(smsMsg)로 수신한 SMS 내용에 접근할 수 있다.

### 3-2. SMS 발신 블록 추가

3-1에서 SMS을 특정 번호로 수신하여 메시지를 특별 식별자로 구분할 수 있게 되었다. 이제 수신한 SMS 내용을 이제는 내가 포워딩하고 싶은 다른 번호로 보내는 작업을 수행한다.

이를 위해서 3-1에서 한 것과 같이 Send SMS 블록을 추가하고 SMS received 블록과  Send SMS 블록을 연결해 준다.

![SMS 발신 블록 추가](/img/2019-10-29-SMS-Forwarding/8.jpg){:width="35%"}

위 처럼 블록을 추가하고 연결하였으면 3-1에서 한 것처럼 해당 블록이 수행할 내용을 설정해야한다. 이를 위해서 Send SMS 블록을 클릭한다.

![SMS 발신 블록 설정](/img/2019-10-29-SMS-Forwarding/9.jpg){:width="35%"}

Send SMS 블록에서 설정해줄 내용은 PHONE NUMBER와 MESSAGE 항목이다. 3-1에서 했던 것과 비슷하다. 여기서 PHONE NUMBER는 SMS을 발신하게 될 대상을 입력하고 MESSAGE 항목에는 3-1에서 입력한 메시지 식별자(3-1에서는 smsMsg)를 입력하면 된다.

> 0319998888에 SMS을 포워딩(송신)하고 싶다면 PHONE NUMBER에 해당 번호를 입력한다. MESSAGE 항목의 오른쪽에서 fx를 누르면 '='이 추가되며 smsMsg입력할 때 자동완성 기능이 제공된다.

### 3-3 반복 설정 

해당 기능들은 플로우 차트로 진행되므로 수신되는 SMS 지속적으로 포워딩하려면 반복 설정이 필요하다. 이는 Send SMS -> SMS received 블록을 연결하여 순환 구조를 만들어 주면 된다.

![순환 구조 설정](/img/2019-10-29-SMS-Forwarding/10.jpg){:width="35%"}

위 처럼 SMS Send의 OK와 SMS recevied의 IN을 연결하면 순환 구조가 완성 된다.

### 3-4 여러 대상에게 포워딩

내가 수신받은 내용을 한 대상에게만 포워딩 하는 것이 아니라 두 대상 이상에게 포워딩 하고 싶다면 Send SMS 블록을 번호만 다르게하여 추가해 플로우 차트를 구성하면 된다. 아래는 예시 이다.

![추가 설정](/img/2019-10-29-SMS-Forwarding/11.jpg){:width="35%"}

## 4. 실행

3-4 처럼 모든 설정이 완료되었으면 우상단 V 아이콘을 클릭하고 Start 버튼을 누르면 이제부터 포워딩이 수행된다. "제목 없음" 을 클릭해서 이름을 입력할 수 도있다.........

![마무리 실행](/img/2019-10-29-SMS-Forwarding/12.jpg){:width="35%"}