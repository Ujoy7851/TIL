## 콜백

```jsx
// 콜백
Users.findOne('zero', (err, user) => {
	if(err) {
		return console.error(err);
	}
	console.log(user);
	Users.update('zero', 'nero', (err, updatedUser) => {
		if(err) {
			return console.error(err);
		}
		console.log(updatedUser);
		Users.remove('nero', (err, removedUser) => {
			if(err) {
				return console.error(err);
			}
			console.log(removedUser);
		});
	});
});

// 프로미스
Users.findOne('zero')
	.then((user) => {
		console.log(user);
		return Users.update('zero', 'nero')
	})
	.then((updatedUser) => {
		console.log(updatedUser);
		return Users.remove('nero')
	})
	.then((removedUser) => {
		console.log(removedUser);
	})
	.catch((err) => {
		console.error(err);
	});
```

자바스크립트는 비동기 처리를 위해 콜백 함수를 사용한다. 하지만 콜백은 사용이 중첩될수록 가독성이 떨어지고 에러의 예외 처리가 어렵고 실행 순서를 파악하기 어렵다는 단점이 있다(콜백 헬). 이러한 단점을 극복하기 위해 ES6에서는 또다른 비동기 처리 패턴이 프로미스를 도입했다.

동기식 처리 - 태스크가 순차적으로 처리되며 어떤 작업이 수행 중이면 나머지는 대기해야 한다.
비동기식 처리 - 요청을 병렬적으로 처리하여, 태스크가 종료되지 않아도 다음 태스크를 실행한다.

## 프로미스 Promise

### 프로미스 생성

Promise 생성자 함수를 통해 인스턴스화 한다. Promise 생성자 함수는 비동기 작업을 수행할 resolve와 reject 함수를 인자로 전달받는다. 비동기 처리가 성공하면 resolve 함수를 호출해서 비동기 처리 결과를, 실패하면 reject 함수를 호출해서 에러 메시지를 Promise 객체의 후속 처리 메소드로 전달한다.

```jsx
const promise = new Promise((resolve, reject) => {
  // 비동기 작
  if (/* 비동기 작업 성공 */) {
    resolve('result');
  }
  else { /* 비동기 작업 실패 */
    reject('failure reason');
  }
});
```

### 후속 처리 메소드

Promise로 구현된 비동기 함수는 Promise 객체를 반환해야 한다. Promise로 구현된 비동기 함수를 호출하는 측에서는 Promise 객체의 후속 처리 메소드를 통해 비동기 처리 결과 또는 에러 메시지를 전달받아 처리한다.

**then**

성공(resolve)시 첫번째 콜백 함수를, 실패(reject)시 두번째 콜백 함수를 실행한다. then 메소드는 Promise를 반환한다.

**catch**

예외가 발생하면 호출된다. Promise를 반환한다. then의 두번째 콜백 함수가 비동기 처리의 에러만을 캐치하는 반면 catch는 then 메소드 내부에서 발생한 에러도 캐치한다.

### 프로미스 체이닝

비동기 함수의 처리 결과를 가지고 다른 비동기 함수를 호출해야 할 때, 여러 개의 프로미스를 연결하여 사용할 수 있다. 이는 콜백 헬을 해결하는 방법이다.

then에 리턴값이 있으면 다음 then으로 넘어가고, Promise를 리턴하면 resolve나 reject되어 넘어간다.

### Promise.resolve / Promise.reject

존재하는 값을 Promise로 래핑하기 위해 사용

```jsx
const resolvedPromise = Promise.resolve([1, 2, 3]);
resolvedPromise.then(console.log); // [ 1, 2, 3 ]

// 위와 동일
const resolvedPromise = new Promise(resolve => resolve([1, 2, 3]));
resolvedPromise.then(console.log); // [ 1, 2, 3 ]

const rejectedPromise = Promise.reject(new Error('Error!'));
rejectedPromise.catch(console.log); // Error: Error!

// 위와 동일
const rejectedPromise = new Promise((resolve, reject) => reject(new Error('Error!')));
rejectedPromise.catch(console.log); // Error: Error!
```

### Promise.all

프로미스가 담겨 있는 배열 등의 이터러블을 인자로 전달받아 모든 프로미스를 병렬로 처리하고 그 결과를 배열에 담아 resolve하는 새로운 프로미스를 반환한다. 프로미스의 처리가 하나라도 실패하면 즉시 에러를 reject하는 프로미스를 반환한다.

```jsx
Promise.all([
  new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
  new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2
  new Promise(resolve => setTimeout(() => resolve(3), 1000))  // 3
]).then(console.log) // [ 1, 2, 3 ]
  .catch(console.log);
```

### Promise.race

Promise.all이 모든 프로미스를 병렬처리하는 반면, 가장 먼저 처리된 프로미스가 resolve한 결과를 resolve하는 프로미스를 반환한다.

```jsx
Promise.race([
  new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
  new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2
  new Promise(resolve => setTimeout(() => resolve(3), 1000))  // 3
]).then(console.log) // 3
  .catch(console.log);
```



### Reference

[https://poiemaweb.com/es6-promise](https://poiemaweb.com/es6-promise)
[https://www.inflearn.com/course/node-js-교과서/dashboard](https://www.inflearn.com/course/node-js-%EA%B5%90%EA%B3%BC%EC%84%9C/dashboard)