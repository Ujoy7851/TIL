## Async, Await

async와 await는 자바스크립트의 비동기 처리 패턴 중 가장 최근에 나온 문법으로 여러 Promise의 동작으로 동기스럽게 사용할 수 있게 해준다.

기본 문법은 아래와 같다.

```jsx
async function 함수명() {
  await 비동기_처리_메서드_명();
}
```

async 함수는 await을 만나면 함수의 실행을 일시 중지하고 전달 된 Promise의 해결을 기다린 다음 다시 실행을 시작한다. await은 반드시 Promise를 반환해야 한다.

```jsx
// Promise를 반환하는 함수 정의
function fetchItems() {
  return new Promise(function(resolve, reject) {
    var items = [1,2,3];
    resolve(items)
  });
}

// async, await 사용
async function logItems() {
  var resultItems = await fetchItems();
  console.log(resultItems); // [1,2,3]
}

// async, await 사용안함
function logItems() {
  var resultItems = fetchItems();
  console.log(resultItems);
}
```

아래 함수처럼 await을 사용하지 않는다면, 비동기 함수의 처리가 완료되기 전에 다음 코드가 실행되어 오류가 발생할 수 있다.



### Reference
[https://joshua1988.github.io/web-development/javascript/js-async-await/](https://joshua1988.github.io/web-development/javascript/js-async-await/)