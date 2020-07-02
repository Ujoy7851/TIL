## 함수 선언문

```jsx
function add1(x, y) {
	return x + y;
}
```

## 함수 표현식

```jsx
var add2 = function (x, y) {
	return x + y;
}
```

## 화살표 함수

**function** 키워드 없이 간단하게 함수를 선언할 수 있다.

화살표 함수는 익명 함수로만 사용할 수 있기 때문에 함수 표현식을 사용한다. 또한 화살표 함수에는 함수 객체의 **arguments** 프로퍼티가 없다. 따라서 화살표 함수로 가변인자 함수를 구현해야 할 때는 Rest 파라미터를 사용해야 한다.

```jsx
const add3 = (x, y) => {
	return x + y;
}
// or
const add3 = (x, y) => x + y;
// or
const add3 = (x, y) => (x + y);

// 객체 반환시 소괄호를 사용
() => ({a: 1})

const arrowFunc = () => {};
console.log(arrowFunc.hasOwnProperty('arguments')); // false
```

## 화살표 함수에서의 this

함수 선언문과 화살표 함수에서의 **this**는 다르게 바인딩 된다.

[this 설명 부분 참고](https://www.notion.so/Javascript-d26aa4bf54874acd9650c4dc702e8684)

- 생성자 함수와 메소드를 제외한 모든 함수(콜백 함수, 내부 함수 등)의 **this**는 전역 객체를 가리킨다.
- 화살표 함수의 **this**는 항상 상위 스코프의 **this**에 바인딩 된다. (Lexical this)

## 화살표 함수를 사용하면 안되는 경우

1. 메소드

    ```jsx
    const person = {
    	name: 'Lee',
    	sayHi: () => console.log(this.name)
    };
    person.sayHi();  // undefined
    ```

    메소드로 정의한 화살표 함수의 내부 **this**는 메소드를 소유한 객체가 아닌 상위 컨택스트인 전역 객체 **window**를 가리킨다. ES6의 축약 메소드 표현을 사용해 메소드를 정의하는 것이 좋다.

2. prototype

    ```jsx
    const person = {
      name: 'Lee',
    };

    Object.prototype.sayHi = () => console.log(`Hi ${this.name}`);

    person.sayHi(); // Hi undefined
    ```

    화살표 함수로 메소드를 정의한 경우와 마찬가지이다.

3. 생성자 함수

    생성자 함수는 **prototype** 프로퍼티가 가리키는 프로토타입 객체의 **constructor**를 사용해 객체를 생성한다. 하지만 화살표 함수는 **prototype** 프로퍼티를 가지고 있지 않다.

    ```jsx
    const Foo = () => {};

    console.log(Foo.hasOwnProperty('prototype')); // false

    const foo = new Foo(); // TypeError: Foo is not a constructor
    ```

4. **addEventListener**의 콜백 함수

    **this**가 상위 컨택스트인 전역 객체를 가리키기 때문에 이벤트 리스너에 바인딩된 요소를 조작할 수 없다.

    ```jsx
    // Bad
    var button = document.getElementById('myButton');

    button.addEventListener('click', () => {
      console.log(this === window); // => true
      this.innerHTML = 'Clicked button';
    });

    // Good
    var button = document.getElementById('myButton');

    button.addEventListener('click', function() {
      console.log(this === button); // => true
      this.innerHTML = 'Clicked button';
    });
    ```



### Reference

---

[https://poiemaweb.com/es6-arrow-function](https://poiemaweb.com/es6-arrow-function)