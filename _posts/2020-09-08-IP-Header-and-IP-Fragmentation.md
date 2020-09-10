---
layout : post
title : "IP Header와 IP Fragmentation"
---

&nbsp;&nbsp; IP(Internet Protocol)는 Network 계층에서 Internet을 구현하는 프로토콜이다. IP는 크게 Addressing과 Fragmentation 기능을 통하여 작동된다.[1] 이 글에서는 Fragmentation에 대하여 살펴본다. 그리고 IP Header의 구조를 살펴보고, Fragmentation과 관련된 취약점에 대해 살핀다.

# IP Header의 구조
&nbsp;&nbsp;IP Header의 구조는 그림 1과 같다. 

  <img src='https://t1.daumcdn.net/cfile/tistory/2446013E5232F78705'> 그림 1: IP Header의 구조 [1,2] </img>

  그림 1이 설명하는 각각의 내용은 다음과 같다.

  1. Version : IP의 Version을 나타낸다. IP는 IP주소의 길이에 따아 IPv4(4바이트)와 IPv6(6바이트)로 나뉜다. 그림 1은 IPv4 헤더의 구조를 나타낸 것이다.[1] IPv4 헤더에서 Version은 "0100(2)"로 표기된다.
  2. IHL(Internet Header Length) : IP 헤더의 길이를 4바이트 단위로 나타낸다. [1] 가령, IP 헤더의 길이가 20바이트라면, 20(바이트)/4(바이트) = 5(워드)이므로 길이값은 "0101(2진수) = 5(10진수)" 이다. IHL의 필드의 길이는 4비트이다.
  3. TOS(Type of Service) : IP를 통한 서비스의 우선순위와 요구 품질에 관한 Parameter이다. 가장 왼쪽비트부터 3비트는 우선순위, 네번째 비트부터 Delay 개선, Throughput 개선, Relibility 개선에 관한 값이다. 그 이후의 비트값은 0으로 남겨두었다. [1] 각각의 서비스에 따라 정해진 값이 있으며, 대부분의 IP 패킷은 TOS가 0으로 설정된다.[2]
  4. Identification : IP 패킷을 식별하는 값으로, 길이는 2바이트이다. IP Fragmentation으로 분할된 패킷들을 재조합하는데 사용한다. [1,2]
  5. IP Flag : IP Flag는 Fragment의 가능성과 그 유무를 나타낸다[1]. 첫 비트는 항상 0으로 설정하고 두번째 비트부터 Fragmentation의 가능성(0이면 가능, 1이면 불가능이다.), 패킷 이후에 Fragment가 있는지 (0이면 '마지막 조각임', 1이면 '이후에 조각이 더 있음')를 나타낸다.[1,2]
  8. Fragment Offset : 분할된 조각의 상대위치를 8바이트 단위로 나타낸다. 첫번째 조각의 offset이 0이다. 
  6. TTL(Time to Live) : TTL은 패킷의 유효시간(수명)을 나타내는 값으로, 패킷이 라우팅될 수 있는 최대 횟수를 나타낸다[1,2].<sup id="a1">[1](#f1)</sup> 패킷이 라우팅되면 TTL의 값은 1씩 차감되며, TTL=0인 패킷은 소멸한다.
  7. Protocol : 상위 계층(전송 계층)에서 사용하는 프로토콜을 식별하는 번호이다.[1] 대표적인 값으로 6이 있고, 이는 TCP(Transmission Control Protocol)을 나타낸다.<sup id="a2">[2](#f2)</sup> 
  9. Header Checksum : 전체 헤더값이 유효한지 나타내는 값이다.
  10. IP Address : Source Address에는 보내는 측의 IP 주소, Destination Address에는 보내고자 하는 곳의 IP 주소가 들어간다. IPv4의 주소는 0 ~ 255까지의 십진수를 기준으로 점('.')으로 구별되나, 패킷에서는 연속된 비트값으로 저장된다. 
  11. IP Option : 추가적인 IP 패킷의 설정값들이다.   

# IP Fragmentation와 그 취약점

&nbsp;&nbsp;IP 패킷은 Data Link 계층에 전송될 때, 이더넷 프레임의 Payload에 적재된다. 이 때, 이더넷 프레임의 payload의 최대 용량을 1500 바이트이다. IP 헤더의 최소 용량이 20 바이트[1]이므로, 하나의 IP 패킷으로 보낼 수 있는 최대 용량을 1480 바이트이다. 

&nbsp;&nbsp;IP Fragmentation은 이러한 특성에도 불구하고 큰 용량의 데이터를 전송하기 위하여 이용되는 주요 기능이다. 1480바이트보다 큰 용량의 데이터를 IP 패킷으로 보내려고 하면, 1480바이트 단위로 데이터를 분할한 후, 분할된 조각들에 헤더를 복사하고, Offset을 붙여 전송한다.[1,3] 분할된 조각들은 수신자에 의하여 재조립된다.

&nbsp;&nbsp; IP Fragmentation은 재조립하는 과정에서 조각들을 확인하지 않아 취약하다. 이를 이용한 공격방법으로는 Tiny Fragment Attack이 있다. [3] Tiny Fragment Attack은 TCP 헤더의 Port값이 보이지 않도록 분할하여 전송한다. 수신자 측은 TCP 헤더의 포트의 존재 유무를 확인하지 않고 조각들을 조합하므로, 공격자가 원하는 포트에 데이터를 전송할 수 있다. [4] 



<b id="f1">1 </b>TTL의 값은 대개 송신측의 운영체제에 의해 정해지며(리눅스에서는 64, 윈도우에서는 128로 정해져 있음이 알려져 있다.)

<b id="f2">2 </b>프로토콜별로 정해진 ID를 자세히 알아보려면, <https://tools.ietf.org/html/rfc790> 의 5페이지부터 참조하면 된다.

# 참고문헌
[1] Postel, J., "Internet Protocol", STD 5, RFC 791, DOI 10.17487/RFC0791, September 1981, <https://www.rfc-editor.org/rfc/rfc791.txt>.

[2] Mind Net. 2020. [ 네트워크 쉽게 이해하기 18편 ] IP Header IP헤더 구조. [online] Available at: <https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-18%ED%8E%B8-IP-Header-IP%ED%97%A4%EB%8D%94-%EA%B5%AC%EC%A1%B0> [Accessed 8 September 2020].

[3] 2016. IP Fragmentation을 이용한 공격. [online] Available at: <https://kkn1220.tistory.com/76?category=598022> [Accessed 8 September 2020].

[4] 정현철, 2001, 
IP Fragmentation을 이용한 공격기술들. [online] Available at: <https://www.linux.co.kr/security/certcc/IP%20Fragmentation.htm> [Accessed 8 September 2020].