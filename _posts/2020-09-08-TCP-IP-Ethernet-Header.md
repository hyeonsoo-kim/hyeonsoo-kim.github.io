---
layout : post
title : "Ethernet Frame"
categories : ["Network"]
---

&nbsp;&nbsp; 이더넷 프레임(Ethernet Frame)은 Data Link Layer에서 이더넷 기술에 기반한 물리 계층에 데이터를 전송하기 위한 데이터 단위이다. 그림 1은 이더넷 프레임의 구조이다. 이더넷 프레임은 Preamble, SFD(Start Frame Delimeter), MAC Address, EtherType, Payload, FCS로 구성한다. 

<center><img src='https://upload.wikimedia.org/wikipedia/commons/thumb/4/42/Ethernet_frame.svg/1920px-Ethernet_frame.svg.png'>그림 1: Ethernet Frame의 대략적인 구조 [1]</img></center>

## Preamble과 SFD
&nbsp;&nbsp;IEEE 802.3 규격에서는 Preamble과 SFD를 분리하여 정의하고 있으나, Ethernet 규격에서는 Preamble과 SFD를 분리하지 않고 상위 56비트는 "10101010"을 7회 반복하고, 하위 8비트는 "10101011"로 한다.[2] Preamble과 SFD는 실제의 Ethernet Data를 나타내지는 않고, 송/수신 장비를 동기화하기 위하여 사용된다. [2,3]

&nbsp;&nbsp;먼저, Preamble은 송/수신 측의 전송 속도를 맞추기 위하여 사용되는 부분으로, 56비트의 길이를 가진다.[3] 일반적으로 Preamble은 비트패턴 "10101010"이 7회 반복되는 것으로 생성된다. [1,3]  SFD는 Frame이 시작됨을 나타내는 비트패턴이다. "10101011" 로 이루어진다. [1] 

## MAC Address
&nbsp;&nbsp; Preamble과 SFD 다음에 오는 것은 MAC 주소들이다.  실제 Ethernet Frame은 MAC Address들로부터 시작한다. 

&nbsp;&nbsp;MAC(Media Access Control) 주소는 장치에 있는 NIC(Network Interface Card)의 고유 주소이다. MAC은 6바이트의 길이를 가지고 있다. 상위 3바이트는 제조사별 코드, 하위 3바이트는 각 NIC별 코드이다.

 &nbsp;&nbsp;이더넷 프레임은 MAC 주소를 Destination Address, Source Address의 순으로 결합한다. Destination Address는  상대 NIC의 MAC 주소, Source Address는 데이터를 송신하는 NIC의 MAC 주소이다. 그림 2는 Wireshark를 이용하여 캡쳐한 패킷의 일부를 나타낸 것이다. 

 <center><img src='ethernet_packaet.png'>그림 2: Wirshark를 이용하여 캡쳐한 패킷에서 분석된 Ethernet 프레임 : 가상 상단은 캡쳐된 패킷의 목록, 중간 부분은 패킷을 가독성있게 나타낸 것, 아래는 Hex로 표현한 부분이다.</img></center> 

 &nbsp;&nbsp;그림 2의 가장 아랫부분을 살피면, Hex값은 "085dddba6070", "34415d3ba71d" 순으로 이어진다. 그림 2의 중간부분을 살펴보면, Destination은 "08:5d:dd:ba:60:70", Source는 "34:41:5d:3b:a7:1d"이다. 콜론(:)으로 표기된 것은 MAC주소를 2자리의 HEX로 끊어 표기하는 방법에 따른 것이다.

## EtherType과 Length 그리고 Payload
&nbsp;&nbsp; EtherType과 Length는 16비트 길이의 필드를 공유하고 있다. EtherType은 이더넷 프레임을 통하여 전송하는 데이터가 어느 프로토콜의 데이터인지 구별하는 것이다.[2,3] 그림 2의 중간부분의 Type의 값은 0x0800이고, 이는 IP(Internet Protocol)을 나타낸다.

&nbsp;&nbsp; 만일, EtherType과 Length는 0x6000 이하이면 EtherType, 그러하지 않으면 Length로 구별한다. Length는 이더넷 프레임으로 전송하는 데이터의 길이를 뜻한다.[1,2,3] 

&nbsp;&nbsp; Payload는 이더넷 프레임으로 전송하려는 실제 데이터이다. 최소 46바이트, 최대 1500 바이트의 데이터까지 수용 가능하다. Payload에는 데이터 상위 계층(Network Layer, Transmission Layer 등)으로부터 캡슐화된 데이터들이 포함된다. 만일, Payload의 길이가 46바이트 미만인 때는 그 차이만큼 0으로 Padding하여 46바이트를 맞춘다.[3]

## FCS
FCS(Frame Check Sequence)는 이더넷 프레임이 정확하게 생성된 것인지 확인하기 위한 필드이다. FCS는 MAC주소, EtherType/Length, Payload들을 이용하여 CRC(Cyclic Redundancy Check) 방식의 규칙에 따라 Encoding한다.[1]

## 참고문헌
[1] IEEE Standard for Ethernet," in IEEE Std 802.3-2015 (Revision of IEEE Std 802.3-2012) , vol., no., pp.1-4017, 4 March 2016, doi: 10.1109/IEEESTD.2016.7428776.

[2] Mind Net. 2020. [ 네트워크 쉽게 이해하기 17편 ] Frame Header (Ethernet II, IEEE802.3) 구조. [online] Available at: <https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-17%ED%8E%B8-Frame-Header-Ethernet-II-IEEE8023-%EA%B5%AC%EC%A1%B0> [Accessed 8 September 2020].

[3] 네이버 블로그 | ♥오리뎅이의 네트워크 사랑방♥. 2020. [오리뎅이의 네떡야그 - 9] Ethernet Frame 이건 꼭 아셔야죠!. [online] Available at: <https://m.blog.naver.com/PostView.nhn?blogId=goduck2&logNo=221098678152&proxyReferer=https:%2F%2Fwww.google.com%2F> [Accessed 8 September 2020].