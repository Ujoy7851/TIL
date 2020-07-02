## 비구조화 할당(Destructuring)

비구조화 활당 또는 디스트럭처링은 구조화된 배열 또는 객체를 비구조화, 파괴하여 개별 변수에 할당하는 것을 의미한다.

## 배열 디스트럭처링

```jsx
// ES5
var arr = [1, 2, 3];
var one   = arr[0];
var two   = arr[1];
var three = arr[2];
console.log(one, two, three); // 1 2 3

// ES6
const arr = [1, 2, 3];
const [one, two, three] = arr;
console.log(one, two, three); // 1 2 3

[x, , z] = [1, 2, 3];
console.log(x, z); // 1 3

[x, y = 10, z = 3] = [1, 2];
console.log(x, y, z); // 1 2 3

// 년도, 월, 일 추출 예시
const today = new Date(); // Tue May 21 2019 22:19:42 GMT+0900 (한국 표준시)
const formattedDate = today.toISOString().substring(0, 10); // "2019-05-21"
const [year, month, day] = formattedDate.split('-');
console.log([year, month, day]); // [ '2019', '05', '21' ]
```

## 객체 디스트럭처링

```jsx
// ES5
var obj = { firstName: 'Ungmo', lastName: 'Lee' };
var firstName = obj.firstName;
var lastName  = obj.lastName;
console.log(firstName, lastName); // Ungmo Lee

// ES6
const obj = { firstName: 'Ungmo', lastName: 'Lee' };
// 프로퍼티 키를 기준으로 할당되기 때문에 순서는 의미 없다.
const { lastName, firstName } = obj;
console.log(firstName, lastName); // Ungmo Lee

// 프로퍼티 키가 prop1인 프로퍼티의 값을 변수 p1에 할당
// 프로퍼티 키가 prop2인 프로퍼티의 값을 변수 p2에 할당
const { prop1: p1, prop2: p2 } = { prop1: 'a', prop2: 'b' };
console.log(p1, p2); // 'a' 'b'
console.log({ prop1: p1, prop2: p2 }); // { prop1: 'a', prop2: 'b' }
```

객체 디스트럭처링은 객체에서 특정 프로퍼티 값만을 추출할 수 있다.

```jsx
const todos = [
  { id: 1, content: 'HTML', completed: true },
  { id: 2, content: 'CSS', completed: false },
  { id: 3, content: 'JS', completed: false }
];

// todos 배열의 요소인 객체로부터 completed 프로퍼티만을 추출한다.
const completedTodos = todos.filter(({ completed }) => completed);
console.log(completedTodos); // [ { id: 1, content: 'HTML', completed: true } ]
```

중첩 객체는 아래와 같이 사용한다.

```jsx
const person = {
  name: 'Lee',
  address: {
    zipCode: '03068',
    city: 'Seoul'
  }
};

const { address: { city } } = person;
console.log(city); // 'Seoul'
```



### Reference

---

[https://poiemaweb.com/es6-destructuring](https://poiemaweb.com/es6-destructuring)