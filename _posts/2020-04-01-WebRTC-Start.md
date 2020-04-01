---
layout: post
title: WebRTC 시작하기
category: Media
---

* 작성중인 포스트

# WebRTC Study

* ref
  * [html5rocks manual](https://www.html5rocks.com/ko/tutorials/webrtc/basics/)
  * [codelabs webrtc](https://codelabs.developers.google.com/codelabs/webrtc-web/index.html?index=..%2F..index)
  * <http://jaynewho.com/post/36>

## WebRTC API

### MediaStream(getUserMedia)

미디어 데이터 스트림에 접근하기 위해 사용함. 마이크, 카메라, 화면 캡처 등

### RTCPeerConnection

암호화 및 대역폭 관린, 연결을 담당. 채널을 추상화

### RTCDataChannel

미디어 데이터외 json/text 등 데이터들을 주고 받는 채널을 추상화

## WebRTC 어플리케이션의 수행 단계

### 1.Fetching

MediaStream, getUserMedia 를 이용해 미디어 데이터를 가져옴.

### 2.Signaling

각 Peer들이 서로를 찾기 위한 중간 매개자 역할을 하는 Signaling Server가 필요함. 구현 방식에 제약이 없음.

A가 통신을 요청. B가 통신을 받는다고 가정

#### 2-1. Network 정보를 교환.

* ICE Candidate를 찾음. (Peer들의 ip port 정보 교환 내것도 포함.)

1. A가 RTCPeerConnection Object를 생성, RTCPeerConnection.onicecandidate 핸들러를 통해 내 Client의 Ice Candidate가 확보되면 실행될 callback을 등록

2. 내(A) Candidate가 확보되면 Signaling 서버를 통해 상대(B)에게 내 candidate 정보를 전달(onicecandidate 콜백에서 수행. 확보가 되는게 setLocalDesciprtion이 호출 된 후인가?????? 2-2-2... 콜백등록을 하는거니까 호출 후인가봄...)

3. Signaling 서버로 부터 Peer의 Cadidate가 도착하면 RTCPeerConnection addIceCandidate 를 통해 Peer의 Candidate 정보를 등록. (A, B 둘 다 수행)

#### 2-2. 사용 가능한 미디어 정보들을 교환.(해상도, 코덱 등)

* sdp (Session Description Protocol) 형식을 따르는 blob인 offer와 answer를 주고 받음.

1. 먼저 통신을 요청한 쪽(A)에서 RTCPeerConnection.createOffer를 호출해 사용가능한 코덱, 해상도 등을 추출해서 등록된 콜백에 해당 정보(RTCSessionDescription 객체)를 전달.

2. 콜백 내부에 전달된 정보를 RTCPeerConnection.setLocalDescription 함수를 통해 자신(A)의 description을 설정(setLocalDescription이 호출 되기 전까지는 candidates 수집이 시작되지 않음.)

3. 자신의 미디어 정보를 담은 offer sdp를 생성해 Signaling 서버를 통해 B에게 전달.

4. B는 상대의 offer sdp를 받으면 RTCPeerConnection.setRemoteDescription 함수에 offer sdp를 RTCSessionDescription 객체로 넘겨 설정.

5. B는 RTCPeerConnection.createAnswer 함수에 위에서 설정한 RTCPeerConnection.remoteDescription과 콜백을 전달. html5rocks 예제에서 이 콜백은 createOffer와 같은 콜백으로 콜백은 RTCSessionDescription 객체를 받아 setLocalDescription 함수를 호출해 자신의 description을 설정하고 A에게 answer sdp를 signaling 서버를 통해 전달.

6. offer/answer sdp가 오고가고 양쪽에서 모두 setRemoteDescription 함수가 호출되면 연결이 완료됨.

7. 네트워크와 미디어 정보 교환/획득은 동시에 완료됨. 미디어 스트리밍 전에 이 과정이 완료가 되어야 함.

### 3.Connection

#### 3-1. RTCPeerConnection

![automate 초기 실행 화면](/img/2020-04-01-WebRTC-Start/webrtcArchitecture.png){:width="35%"}