# HTTP(HyperText Transfer Protocol)

웹 상에서 클라이언트와 서버간 통신을 위한 프로토콜이다. HTTP는 클라이언트와 서버 사이 커넥션을 제공하는 TCP을 기반으로 통신한다.

## **HTTP/1.1**

1996년 1.0 버전이, 1999년에 공식적으로 가장 많이 사용되는 1.1 버전이 출시되었다. HTTP에서는 커넥션 관리가 웹 애플리케이션의 성능에 많은 영향을 끼친다. HTTP/1.x에는 세가지 커넥션 관리 모델을 제공한다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9108218d-1748-43c4-8cf1-e4e8f27e1ec9/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200707%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200707T133811Z&X-Amz-Expires=86400&X-Amz-Signature=4f25bf3553ebcd040dadc011a674fc050f0bc3aa0e3c257cb7b5b5cb1666380b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**shotr-lived connections**에서는 요청을 보낼 때 마다 커넥션을 새롭게 생성해야하고 응답이 도착한 이후에 연결을 닫는 형태로 단기로만 유지한다. 이는 다수의 리소스를 필요로 할때 매우 비효율적이다.

이를 위해 하나의 커넥션을 유지하며 연속적인 요청을 처리할 수 있는 **Persistent connection** 모델이 추가되어 새로운 커넥션을 여는데 필요한 시간을 줄였다. 커넥션은 Keep-Alive 헤더를 사용해 최소 얼마간 열려있어야 하는지를 설정한다.

또한 **Pipelining** 모델은 응답조차 기다리지 않고 연속적인 요청을 보내 네트워크 지연을 더 줄였다.

### 단점

**HOL(Head Of Line) Blocking - 특정 응답의 지연**

Pipelining은 단기 커넥션에 비해 속도가 빠르지만 여전히 큰 문제점이 있다. 예를 들어, 하나의 연결에서 3개의 이미지를 요청한 경우 HTTP의 응답 순서는 다음과 같을 것이다.

|—  a.png —|

                  |—  b.png —|

                                     |—  c.png —|

순서대로 이미지를 요청하고 응답받고 다음 이미지를 요청할 것이다. 만약 이때 첫번째 이미지 요청에 대한 응답이 지연되면 다음 이미지들의 요청은 첫번째 이미지의 응답처리가 끝날때까지 대기하게 된다. 이와 같은 현상을 HTTP의 Head of Line Blocking이라고 한다.

|—————————  a.png —————————|

                                                                            |—  b.png —|

                                                                                              |—  c.png —|

**RTT(Round Trip Time) 증가**

HTTP/1.1에서 connection당 하나의 요청을 처리하기 때문에 매 요청별로 connection을 만들어야 하고, TCP에서 동작하는 HTTP 특성상 3-way handshake가 반복적으로 일어나 불필요한 RTT 증가와 네트워크 지연으로 성능이 저하된다.

**무거운 Header**

사용자가 웹페이지를 방문하면 다수의 http 요청이 발생하는데 이때마다 중복된 헤더값을 전송하게 되며, 해당 domain에 설정된 cookie 정보도 매 요청마다 헤더에 포함되어 전송된다. 심한경우 전송하려는 값보다 헤더가 큰 경우도 있다.

### 개선노력

**Image Spriting**

웹페이지를 구성하는 다양한 아이콘 이미지 파일의 요청횟수를 줄이기 위해 아이콘을 하나의 큰 이미지로 만든 다음, CSS에서 해당 이미지의 좌표를 지정해 표시한다.

**Domain Sharding**

HTTP/1.1의 단점을 극복하기 위해 다수의 Connection을 병렬로 생성해 요청을 보내기도 하지만 브라우저에서 Domain별로 최대 6개의 connection을 제공하기 때문에 근본적인 해결책이 아니다. 이를 위해 나온 기법이 Domain sharding으로, 리소스를 여러개의 domain을 나누어 저장한다.

**Minify CSS/Javascript**

http를 통해서 전송되는 데이터의 용량을 줄이기 위해 CSS, Javascript 코드를 축소하여 적용하기도 한다.

**Data URI Scheme**

HTML 문서내 이미지 리소스를 Base64로 인코딩된 데이터로 직접 기술해 요청 수를 줄인다.

**Load Faster**

스타일시트를 HTML 문서 상위에, 스크립트를 하단에 배치한다.브라우저는 HTML파서와 CSS파서가 각각 DOM과 스타일 규칙을 분석해 결합한 내용을 화면에 표시한다. 두 파서는 동시에 동작하기 때문에 DOM에 디자인이 적용된 상태로 보일 수 있도록 스타일 시트는 HTML 상단에 정의한다. 하지만 스크립트가 실행되는 동안은 문서의 파싱이 중단되기 때문에 스크립트는 하단에 배치하는 것이 좋다. (HTML5에서는 스크립트를 비동기로 처리하는 속성이 추가되어 별도의 맥락에 의해 파싱되고 실행된다.)

---

## HTTP/2

이런 다양한 노력에도 불구하고 HTTP/1.1의 단점을 근본적으로 해결할 수 없었고, 구글에서는 웹페이지의 로드 지연 시간을 줄이는 것을 목표로 SPDY(스피디)라는 프로토콜을 개발했으며, 이는 이후 HTTP/2의 기초가 되었다.

HTTP/2는 이전의 HTTP 표준을 대체하는 것이 아닌 확장하는 것을 목적으로 개발되었다. HTTP 메서드, 상태코드, URI 및 헤더 필드와 같은 핵심 개념은 그대로 유지시키면서, 데이터 서식(프레임)이 지정되는 방식과 데이터가 전송되는 방식을 변경해 이전 프로토콜의 성능 문제였던 지연 시간을 감소시키는 것에 초점을 맞추었다.

HTTP/2에서는 다음과 같은 방식으로 성능을 향상시켰다.

**Binary Framing Layer & Multiplexed Stream**

![https://developers.google.com/web/fundamentals/performance/http2/images/binary_framing_layer01.svg?hl=ko](https://developers.google.com/web/fundamentals/performance/http2/images/binary_framing_layer01.svg?hl=ko)

HTTP/2의 핵심은 바이너리 프레이밍 계층을 사용해 요청과 응답의 멀티플렉싱을 지원한다는 것이다. HTTP 메시지를 바이너리 형태의 프레임으로 나누고 이를 전송하고 받은 쪽에서 다시 조립한다. 하나의 커넥션에서 여러개의 메세지를 동시에 주고받을 수 있어 Pipelining에서 발생했던 HOL 문제가 해결되었다.

관련 용어

- 스트림: 구성된 연결 내에서 전달되는 바이트의 양방향 흐름이며, 하나 이상의 메세지가 전달될 수 있다.
- 메세지: 논리적 요청 또는 응답 메세지에 매핑되는 프레임의 전체 시퀀스
- 프레임: HTTP/2에서 통신의 최소 단위이며 각 최소 단위에는 하나의 프레임 헤더가 포함된다. 이 프레임 헤더는 최소한으로 프레임이 속하는 스트림을 식별한다.

**Stream Prioritization - 스트림 우선순위 지정**

리소스간 의존관계(우선순위)를 설정해 렌더링이 늦어지는 문제를 해결했다.

**Server Push**

서버가 클라이언트가 요청하지 않은 리소스를 보낼 수 있게 하는 방식이다. HTTP/1.1에서는 클라이언트가 요청한 HTML 문서를 수신한 후 HTML 문서를 해석하면서 필요한 리소스를 재요청했지만, HTTP/2에서는 서버에서 클라이언트가 요청하지 않은(HTML에 포함된 리소스)를 push해주는 방식으로 클라이언트의 요청을 최소화해서 성능을 향상시킨다. 이를 PUSH_PROMISE라 하고, PUSH_PROMISE를 통해 서버가 전송한 리소스에 대해 클라이언트는 요청하지 않는다.

**Header Compression**

Header 정보를 압축하기 위해 Header Table과 Huffman Encoding 기법을 사용하는데 이를 HPACK 압축방식이라 부르며 별도의 명세서(RFC 7531)로 관리하고 있다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e8b3a70b-fa77-4c79-b878-08b69c0232bf/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200707%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200707T133840Z&X-Amz-Expires=86400&X-Amz-Signature=363a28e819a577aa8e6909fc4b5adcf52b5102b72c1ce93d34a8df88cf616874&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

위 그림처럼 클라이언트가 두번의 요청을 보낸 경우 HTTP/1.x에서는 Header에 중복값이 존재해도 그냥 중복 전송한다. 하지만 HTTP/2에선 Header에 중복값이 존재하는 경우 Static/Dynamic Header Table 개념을 사용하여 중복 Header를 검출하고, 중복된 Header는 index값만 전송, 중복되지 않은 Header 정보의 값은 Huffman Encoding 기법으로 인코딩 처리 하여 전송한다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/029876c5-a121-48a6-b6c0-5a7d72569b7e/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200707%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200707T133853Z&X-Amz-Expires=86400&X-Amz-Signature=3cd0266bc66c579b1228c03490f881d3d74f3e59d10cbdcc1880d37dc5adf0a2&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

---

## HTTPS

HTTP의 보안이 강화된 버전으로  SSL/TLS 프로토콜을 통해 세션 데이터를 암호화한다.

HTTPS의 기본 TCP/IP 포트는 443이다.

대부분의 브라우저에서 HTTP/2는 HTTPS 기반으로 동작한다.

**관련 용어**

- CA(Certificate Authority)- 클라이언트가 접속한 서버가 클라이언트가 의도한 서버가 맞는지를 보장하는 역할을 보장하는 기관들
- SSL 인증서- CA로부터 신뢰할 수 있는 사이트라는 것을 인증받은 증명서