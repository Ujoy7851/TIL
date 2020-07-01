## Node.js


단순 서버가 아니라 크롬의 V8 엔진으로 빌드된 JavaScript 런타임 환경으로 웹 브라우저가 아닌 환경에서 자바르크립트를 동작시킬 수 있게 해주는 소프트웨어 플랫폼이다.

Node.js는 Non-blocking I/O와 단일 쓰레드 이벤트 루프를 통한 높은 Request 처리 성능을 가지고 있다.

데이터를 실시간으로 처리하여 빈번한 I/O가 발생하는 SPA에 적합하며, CPU 사용률이 높은 어플리케이션에는 권장하지 않는다.

**설치**

[다운로드 | Node.js](https://nodejs.org/ko/download/)

nodejs 설치시 npm도 함께 설치된다.

터미널 실행 후 다음과 같이 설치가 잘 되었는지 확인한다.

```bash
node -v
npm -v
```

**실행**

1. REPL 실행 (Read, Eval, Print, Loop)

    ```bash
    $ node
    > console.log('Hello World');
    Hello World
    ```

    `.exit` 또는 `ctrl + C`를 두번 입력하거나 `ctrl + D`를 입력해 종료시킨다.

2. 자바스크립트 파일 실행

    ```bash
    $ node helloNode.js
    또는
    $ node helloNode
    ```

## 이벤트 루프


![](https://joshua1988.github.io/images/posts/web/translation/how-js-works/js-engine-runtime.png)

싱글 쓰레드 기반의 자바스크립트 엔진은 하나의 코드 조각을 하나씩 실행하는 일을 하고, 비동기적으로 이벤트를 처리하거나 Ajax 통신을 하는 작업은 사실상 Web API에서 모두 처리된다. 작업이 완료되면 요청시 등록했던 callback이 queue에 등록된다.

**이벤트 루프**

반복해서 call stack과 queue 사이의 작업들을 확인하고, call stack이 비워있는 경우 queue에서 작업을 꺼내어 call stack에 넣어 자바스크립트 엔진이 하나씩 처리할 수 있게 준다.

**호출 스택**

작업(함수)이 요청되면 순차적으로 call stack에 쌓이게 되고 순차적으로 실행된다.

**Callback Queue(Task Queue)**

비동기 처리 함수의 콜백 함수, 비동기식 이벤트 핸들러, Timer 함수(setTimeout(), setInterval())의 콜백 함수가 보관되는 영역이다.

### Reference

---

인프런 Node.js 교과서 강의

[https://poiemaweb.com/nodejs-npm](https://poiemaweb.com/nodejs-npm)

[https://velopert.com/133](https://velopert.com/133)

[https://joshua1988.github.io/web-development/translation/javascript/how-js-works-inside-engine/](https://joshua1988.github.io/web-development/translation/javascript/how-js-works-inside-engine/)
