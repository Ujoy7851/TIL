## REST API

### REST(Representational State Transfer)

http에서 필요한 자원에 접근하는 방식을 정해놓은 아키텍쳐

### REST 구성 요소

REST는 자체 표현 구조로 구성되어 REST API만으로 요청을 이해할 수 있다.

- **자원** - 자원은 URI를 통해 식별된다.
- **method** - HTTP 메소드
    - GET - 자원조회
    - POST - 자원 생성
    - PUT - 자원 전체 수정
    - PATCH - 자원의 일부 수정
    - DELETE - 자원 삭제
- **message** - HTTP header와 body, 응답상태코드로 구성되어 있으며, header와 body에 포함된 메시지는 메시지를 처리하기 위해 충분한 정보를 포함한다.
    - body - 자원에 대한 정보를 전달(JSON, XML 등의 포맷)
    - header - body에 담긴 데이터의 포맷 정의. 요청 헤더는 'Accept' 항목으로, 응답 헤더는 'Content-type'으로 포컨텐츠 타입 설명
    - 응답상태코드

### REST 특성

REST는 다음과 같은 스타일을 반드시 지켜야 한다. 여기서 스타일이란 제약조건의 집합을 의미한다.

1. Uniform (유니폼 인터페이스)

    HTTP 표준에만 따른다면, 어떤 기술이라던지 사용이 가능한 인터페이스 스타일이다.

2. Stateless (무상태성)

    사용자나 클라이언트의 컨택스트를 서버쪽에 유지 하지 않는다는 의미로,쉽게 표현하면 HTTP Session과 같은 컨텍스트 저장소에 상태 정보를 저장하지 않는 형태를 의미한다. 때문에 API 서버는 들어오는 요청만을 단순히 처리하면 됩니다. 때문에 서비스의 자유도가 높아지고 서버에서 불필요한 정보를 관리하지 않음으로써 구현이 단순해집니다.

3. Cacheable (캐시 가능)

    REST의 가장 큰 특징 중 하나는 HTTP라는 기존 웹표준을 그대로 사용하기 때문에, 웹에서 사용하는 기존 인프라를 그대로 활용이 가능하다. 따라서 HTTP가 가진 캐싱 기능이 적용 가능하다. HTTP 프로토콜 표준에서 사용하는 Last-Modified태그나 E-Tag를 이용하면 캐싱 구현이 가능하다.

4. Self-descriptiveness (자체 표현 구조)

    REST의 또 다른 큰 특징 중 하나는 REST API 메시지만 보고도 이를 쉽게 이해 할 수 있는 자체 표현 구조로 되어 있다는 것이다.

5. Client - Server 구조

    REST 서버는 API 제공, 클라이언트는 사용자 인증이나 컨텍스트(세션, 로그인 정보)등을 직접 관리하는 구조로 각각의 역할이 확실히 구분되기 때문에 클라이언트와 서버에서 개발해야 할 내용이 명확해지고 서로간 의존성이 줄어들게 된다.

6. 계층형 구조

    REST 서버는 다중 계층으로 구성될 수 있다. 순수 비즈니스 로직을 수행하는 API 서버와 그 앞단에 사용자 인증, 보안, 로드 밸런싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있고 PROXY, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 한다.

    위에서 언급한 것들 대부분은 모두 쉽게 구현가능 하지만 문제는 Uniform Interface이다.

    Uniform Interface의 제약조건

    - 리소스가 URI로 식별되야 합니다.
    - 리소스를 생성,수정,추가하고자 할 때 HTTP메시지에 표현을 해서 전송해야 합니다.
    - 메시지는 스스로 설명할 수 있어야 합니다. (Self-descriptive message)
    - 애플리케이션의 상태는 Hyperlink를 이용해 전이되야 합니다.(HATEOAS)

    위 제약조건 중 self-descriptive message와 HATEOAS가 REST API의 제공을 어렵게 하는 요인이다. 대부분의 서비스가 REST의 모든 것을 지키지 않는 Web API 또는 HTTP API를 제공한다.

### REST API

REST를 통해 서비스 API를 구현한 것

### REST API 설계 규칙

- URI는 자원을 표현해야 한다.
- 자원에 대한 행위는 HTTP 메소드로 표현한다.
- `/`는 계층 관계를 나타내는데 사용한다.
- 마지막 문자로 `/` 를 포함하지 않는다.
- 자원의 정보를 표현하는 URI는 동사보다는 명사로 구성되야 한다.
    - 자원간의 관계는 다음과 같이 표현한다.

    ```
    일반적으로 소유 ‘has’의 관계를 표현할 때
    GET : /users/{userid}/devices

    관계명이 애매하거나 구체적 표현이 필요할 때
    GET : /users/{userid}/likes/devices
    ```

- `_`보다는 `-`을 권장한다.
- 대문자 사용은 피한다.
- 파일의 확장자는 URI에 포함시키지 않는다.

### HTTP 상태 코드

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ca74ecfc-872e-4565-a483-6c89d6e19166/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200707%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200707T133935Z&X-Amz-Expires=86400&X-Amz-Signature=79f8f4f4f8da41bf6a4ecf171d75bbf32cd6afe8ac3d7041d2a02b800a2ca134&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c431d524-4fa9-4a05-91fa-1767fde88cae/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200707%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200707T134015Z&X-Amz-Expires=86400&X-Amz-Signature=239c60737bdfc6e25201f4426495544f347d2b3277b541640cb862aa5930cb57&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a8005b4c-bc80-4a86-9fd9-73bf1da6cb71/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200707%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200707T134022Z&X-Amz-Expires=86400&X-Amz-Signature=b362105c01e878d5ee7e69eb348c60b3e6420b6f62be1a1991ad5add518d7e3a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

### Reference

[https://bcho.tistory.com/953](https://bcho.tistory.com/953)

[https://meetup.toast.com/posts/92](https://meetup.toast.com/posts/92)

[https://www.edwith.org/boostcourse-web/lecture/16740/](https://www.edwith.org/boostcourse-web/lecture/16740/)

[https://www.youtube.com/watch?v=RP_f5dMoHFc](https://www.youtube.com/watch?v=RP_f5dMoHFc)