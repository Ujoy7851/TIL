## global 객체

**global**은 노드의 전역 객체이다. (브라우저에서는 window) 전역 객체는 파일간 공유되며, 어디서든 수정가능하기 때문에 직접 값을 넣어 사용하지 않는 편이 좋다.

전역 객체의 속성들은 `global` 를 생략하고 사용이 가능하며 대표적으로 다음 속성들이 있다.

```
buffer          // 파일과 네트워크 입출력 작업에 사용
console         // 콘솔창에 결과를 출력
module					// 모듈 객체
exports         // 모듈을 다루는 객체
require					// 모듈을 가져오는 객체
process         // 프로세스의 실행에 대한 정보를 다루는 객체
setImmediate		// 타이머 함수
setInterval			// 타이머 함수
setTimeout			// 타이머 함수
stream          // 스트림 기반 데이터 전송
```

** 참고

노드에서 전역 환경의 **this**는 전역 객체인 global이 아니라 module.exports, exports와 같다. 또한 함수 선언문 안에서는 global을 가리키지만, 화살표 함수의 경우는 상위 스코프의 this와 바인딩되므로 module.exports와 같다.

### console 객체

global 객체에 들어있으며 `console.log`를 비롯한 다양한 메소드가 정의되어 있다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/7ab191bb-8661-4584-a922-da083b524738/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134742Z&X-Amz-Expires=86400&X-Amz-Signature=dc8837cbfb3f33c022c3ecdafbcac132851551fcec1b6fbe5b51495ec3abe5aa&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

### 타이머

**setTimeout** : 일정 시간 이후 콜백 함수 실행

**setInterval** : 일정 시간마다 콜백 함수 실행

**setImmediate** : 이벤트 루프로 보내기 위해 사용

```jsx
const timeout = setTimeout(() => {
	console.log('1.5초 후 실행');
}, 1500);

const interval = setInterval(() => {
	console.log('1초마다 실행');
}, 1000);

const im = setImmediate(() => console.log('즉시 실행'));

clearTimeout(timeout); // 타이머 실행 취소
clearInterval(interval);
clearImmediate(im);
```

### process 객체

현재 실행 중인 노드 프로그램에 대한 정보가 담긴 객체이다.

```jsx
process.version // 실행중인 노드의 버전
process.arch // 프로세서 정보
process.platform // 운영체제 정보
process.argv // 프로세스에 전달된 인수 배열
process.pid // 프로세스 아이디
process.cwd() // 현재 작업 디렉터리
process.cpuUsage() // cpu 사용량
process.uptime() // 프로세스 지속시간
process.memoryUsage() // 메모리 사용량
process.exit() // 프로세스 실행 정지
```

## 전역 변수

### __filename, __dirname

```jsx
// 실행한 파일의 전체 경로 출력
console.log('file name : %s', __filename);
//-> file name : /Users/path/to/filename.js

// 실행한 파일이 들어있는 디렉토리의 전체 경로 출력
console.log('dir name : %s', __dirname);
//-> file name : /Users/path/to/dir
```

### Reference

[https://tutorialpost.apptilus.com/code/posts/nodejs/ns-global-object/](https://tutorialpost.apptilus.com/code/posts/nodejs/ns-global-object/)