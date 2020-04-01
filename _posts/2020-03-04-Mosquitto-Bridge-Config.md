---
layout: post
title: Mosquitto Bridge 설정 하기 (mosquitto 이중화 흉내내기)
category: IoT
---

* 작성중인 포스트

MQTT는 IoT 환경에서 사용하기 위한 가벼운 Msg queue Protocol.
Mosquitto는 MQTT를 구현한 많은 구현체 중 하나로 오픈소스이다.
이 포스트에서는 Mosquitto Broker가 여러 대인 경우 이중화를 고민하다가 생각해낸 방법이다.

Mosquitto는 Broker가 여러대일 때 Topic을 공유할 수 있도록 Bridge 기능이 내장되어 있다. 앞단에는 HA-proxy를 두고 요청을 분산시키고 뒷단에서는 Broker간에 Topic을 공유하는 방식을 사용 한다. 아래와 같은 구조로 구성해볼 예정이다.

* 구성도

<pre>
Pub/Sub Client - HA-proxy --- Mosquitto Broker #1 --- Mosquitto Bridge
                           |                       |
                           -- Mosuiqtto Broker #2 --
</pre>

## Mosquitto 설정

1. 위 구성도에서 Mosquitto Broker #1, #2와 Mosquitto Bridge에 Mosquitto를 설치한다.

2. Bridge를 위해서 Mosuiqtto Bridge 노드에 별도 설정은 필요없으며 Mosquitto Broker 두 대에 아래와 같은 설정이 필요하다. (사실 Bridge도 Broker임.)

~~~conf
# /etc/mosquitto/mosquitto.conf 파일 수정 (설정파일 경로가 다를 수 있음.)
connection $BRIDGE_NAME
address $BRIDGE_NODE_IP:$BRIDGE_NODE_PORT
notifications false
topic # both 2 "" ""
~~~

connection : 브릿지 설정을 하겠다는 옵션으로 뒤에 오는 $BRIDGE_NAME은 아무 이름이나 설정이 가능하다.

address : 브릿지 노드의 ip와 port

notifications : false로 지정할 시 브릿지 노드에서 허락하지 않은 pub을 방지한다. 보통 연결해제나 연결 메시지와 같은 내용이다. true로 지정될 경우 notification msg가 설정된 브릿지 상태를 로컬 혹은 원격의 broker로 전달한다.

topic : 토픽에 대한 설정을 하는 옵션이다. #은 모든 토픽에 대해 살피겠다는 의미이다. both는 방향을 의미하는 것으로 pub/sub 모두를 의미한다(in/out 설정이 가능). 2는 QoS를 의미한다. 네번째/다섯번쨰 파라미턴인 ""은 local prefix/remote prefix로  Topic을 Remapping하는데 이용한다. (자세한 내용은 http://www.steves-internet-guide.com/mosquitto-bridge-configuration/ 참고)

위 와 같이 설정한 후, 세 노드를 동작시키면 Broker 1에서 pub한 내용을 Broker2에서 sub하여 확인 가능하다.

## Ha-Proxy 설정

frontend, backend static, backend app 섹션 주석 처리
defaults 섹션 option forwardfor 주석 처리


balance 옵션 : RoundRobin, leastconn(접속수가 가장 적은 서버로)
- static-rr 서버에 weight 가중치를 줘서 , 부여된 가중치에 따라서 연결 된다.
- source: 소스 아이피 기준으로 해싱 하여 연결된다.
- balance url_param: HTTP GET 요청에 대해서 특정 패턴이 있는지 여부 확인 후  조건에 맞는 서버로 분배(조건 없는 경우 round robin으로 처리)
- balance hdr: HTTP 헤더 에서 hdr(<name>)으로 지정된 조건이 있는 경우에 대해서만 분배 (조건 없는 경우 round robin으로 처리)

bind가 안됨 아래 명령어 실행
```bash
setsebool -P haproxy_connect_any=1 
```

로깅 Centos7 
/etc/rsyslog.conf에서 아래 주석 해제
# Provides UDP syslog reception 
$ModLoad imudp
$UDPServerRun 514


/etc/rsyslog.d/haproxy.conf 만들고 local2.*        /var/log/haproxy.log 추가 
systemctl restart rsyslog
systemclt restart haproxy
