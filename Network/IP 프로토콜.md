## IP 프로토콜

OSI 7계층에서의 네트워크 계층, TCP/IP 4계층의 인터넷 계층에서 사용되는 데이터 전송 프로토콜로 호스트간의 통신만을 담당한다.


### IP 프로토콜의 특징

- 비연결형(Connectionless) : 커넥션을 유지하지 않고 데이터를 보내는 방법으로 데이터들이 독립적으로 보내지고 도착 순서가 바뀔 수 있다.
- 비신뢰성(Unreliable) : 데이터가 올바르게 전송된다는 보장이 없는 BestEffort 방식이다. 신뢰성은 TCP 계층에 의존한다.

### 인터넷 계층의 다른 프로토콜

- ICMP 프로토콜

    IP 프로토콜이 신뢰성을 보장하지 않기 때문에 인터넷 계층에서는 ICMP 프로토콜을 사용해 오류가 발생하면 수신측에서 송신측으로 네트워크 상태를 알린다.

- ARP 프로토콜

    이더넷 또는 무선 랜으로 데이터를 보내기 위해서는 수신측은 MAC 주소가 필요하다. ARP는 IP 주소를 MAC 주소로 매핑해주는 역할을 한다.

### IP 헤더

인터넷 계층에서 사용되는 패킷에는 IP 프로토콜로 패킷 전송을 제어하기 위해 데이터 앞에 IP 헤더가 붙는다.

![http://mars.netanya.ac.il/~unesco/cdrom/booklet/HTML/NETWORKING/IMAGES/ipheader.gif](http://mars.netanya.ac.il/~unesco/cdrom/booklet/HTML/NETWORKING/IMAGES/ipheader.gif)

- version : IPv4인지 IPv6인지 구별
- IHL : 헤더의 길이 (20Byte ~ 60Byte)
- TOS : 요구되는 서비스 타입
- Total Length : 헤더와 데이터를 포함한 IP 패킷의 크기
- Identification : 데이터 링크 계층에서 조각화된 패킷을 복원하기 위한 식별자로 동일 fragment에서는 같은 값을 가진다.
- Flag : 패킷 분할과 관련된 필드로 첫번째 bit는 무조건 0이며 나머지 3bit으로 패킷을 분할할 것인지, 마지막 패킷인지를 나타낸다.
- Fragment offset : 분할된 fragment가 원 데이터에서 어떤 위치였는지 나타내는 값
- TTL : 패킷이 네트워크 상에 얼마나 오래 살아남을 수 있는지를 알려주는 값으로 라우터를 통과할 때마다 1씩 줄어준다. 0이 되면 패킷은 버려지고 ICMP 프로토콜을 통해 결과를 출발지에 알린다.
- Protocol : 전송계층의 프로토콜이 무엇인지 나타낸다.
- Header checksum : IP 헤더가 손상되지 않았음을 보증하기 위한 값
- Source IP address : 출발지 IP 주소
- Destination IP address : 도착지 IP 주소. IP 프로토콜이 비연결형 특징을 가지기 때문에 패킷은 항상 출발지와 목적지의 IP 주소를 갖는다.
- Options : 테스트나 디버그 용으로 사용

### IP 주소

인터넷 계층에서 각 호스트를 특정하기 위한 식별자로 사용되는 논리적인 주소이다. IP 주소는 고유하며, 동일 IP를 가진 장비는 존재할 수 없다. 일반적으로 호스트는 하나 이상, 라우터는 2개 이상의 IP 주소를 가진다.

**IPv4**

- 4Bytes(32bits)로 구성되며, 2^32(약 43억)개의 주소를 가진다.
- 0.0.0.0 ~ 255.255.255.255
- 10진수 형태의 .으로 구분한 4개의 옥텟으로 표기한다.
- Network ID, Host ID, IP Prefix로 구성된다.
- 예전에는 IP를 할당할 때 클래스를 나누어서 할당하였는데 IP 주소의 소모가 빨라지자 CIDR(사이다라고 발음) 방식으로 할당하게 되었다.
- IPv4 예약어
    - 0.0.0.0 ~ 0.255.255.255(0.0.0.0/8): IP 주소를 할당받기 전 임시로 사용하는 주소
    - 127.0.0.0 ~ 127.255.255.255(127.0.0.0/8) : 자기 자신을 가리킴
    - 255.255.255.255 : 브로드캐스트 주소로 같은 서브넷에 있는 모든 호스트에게 메시지를 전달한다.
    - 10.0.0.0 ~ 10.255.255.255 (10.0.0.0/8) : A 클래스 사설 IP 주소
    - 172.16.0.0 ~ 172.31.255.255 (172.16.0.0/12) : B 클래스 사설 IP 주소
    - 192.168.0.0 ~ 192.168.255.255 (192.168.0.0/16) : C 클래스 사설 IP 주소

**IPv4 클래스 분류**

![](https://t1.daumcdn.net/cfile/tistory/99068D495BE8101D34)

IPv4 주소는 대역에 따라 A, B, C 클래스 등으로 나눌 수 있다. 각 클래스 안에서 IP는 Network ID와 Host ID로 구분된다. 하나의 로컬 네트워크에서 IP 주소의 Network ID는 동일하며 Host ID는 각각 다르다. Host ID가 모두 1인 경우는 브로트캐스트 주소로 사용된다.

IPv4는 초기에 클래스로 나누어 IP를 할당했지만, 비효율적인 방식이었기 때문에 서브넷을 사용해 IP를 할당하는 CIDR 방식을 사용하게 되었다.

**서브넷, 서브넷 마스크**

서브넷은 하나의 네트워크가 분할되어 나눠진 작은 부분망으로 네트워크를 분할하는 것을 서브네팅이라고 한다. 서브넷팅은 서브넷 마스크를 통하여 수행된다.

서브넷 마스크 값은 32bit의 이진수로 IP 주소와 AND 연산하여 네트워크 부분을 계산한다. 예를 들어, A 클래스의 IP가 116.81.97.8인 경우 서브넷 마스크가 255.0.0.0이므로 Network ID는 116.0.0.0이다.

서브넷 마스크는 prefix를 사용해 표현할 수도 있는데 이는 서브넷 마스크에서 1인 bit의 수를 의미한다. 192.168.3.19/24를 예로들면 서브넷 마스크가 255.255.255.0이므로 Network ID는 192.168.3이다.

서브넷 마스크값에따라 서브넷에서 가질 수 있는 호스트의 수가 달라지므로 효율적으로 IP를 할당할 수 있다.

**CIDR**

클래스 구분을 무시하고 임의의 길이로 서브넷 마스크를 사용해 라우팅하는 방식이다.

**IPv6**

- 16Bytes(128bits)
- 16진수 형태의 :로 구분한 8개의 옥텟으로 표기한다.

**DHCP** (동적 호스트 구성 프로토콜)

호스트의 IP 주소를 자동으로 제공해주는 프로토콜이다. IP 주소뿐만 아니라 서브넷 마스크, 게이트웨이 주소 같은 추가 정보도 제공한다. 네트워크에서 사용되는 IP 주소를 DHCP 서버가 중앙집중식으로 관리하는 클라이언트/서버 모델을 사용한다. 호스트 수가 많거나 빈번하게 접속하고 떠나는 경우에 효율적이다.

### Reference

[https://ddooooki.tistory.com/17](https://ddooooki.tistory.com/17)

[http://blog.naver.com/PostView.nhn?blogId=ljh0326s&logNo=220881753385&categoryNo=13&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView&userTopListOpen=true&userTopListCount=5&userTopListManageOpen=false&userTopListCurrentPage=1](http://blog.naver.com/PostView.nhn?blogId=ljh0326s&logNo=220881753385&categoryNo=13&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView&userTopListOpen=true&userTopListCount=5&userTopListManageOpen=false&userTopListCurrentPage=1)