## Rest

Rest 파라미터(나머지 매개변수)는 매개변수 이름 앞에 `...` 을 붙여 정의한 매개변수를 의미하며, 함수에 전달된 인수들의 목록을 배열로 전달받는다. 비슷한 개념의 **arguments** 객체의 경우 유사배열 객체이기 때문에 배열 메소드를 사용하려면 **Function.prototype.call**을 사용해야만 한다.

Rest 파라미터는 반드시 마지막 파라미터여야 하며, 함수 객체의 **length** 프로퍼티에 영향을 주지 않는다.

```jsx
// Rest 파라미터
function foo(param, ...rest) {
  console.log(Array.isArray(rest)); // true
	console.log(param); // 1
  console.log(rest); // [ 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);

// arguments
function sum() {
  var array = Array.prototype.slice.call(arguments);
  return array.reduce(function (pre, cur) {
    return pre + cur;
  });
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```


## Spread

Spread 문법은 대상을 개별 요소로 분리한다. Spread 문법의 대상은 이터러블(배열, 문자열, Map, Set)이어야 한다.

```jsx
console.log(...[1, 2, 3]) // 1, 2, 3

console.log(...'Hello');  // H e l l o

console.log(...new Map([['a', '1'], ['b', '2']]));  // [ 'a', '1' ] [ 'b', '2' ]

console.log(...new Set([1, 2, 3]));  // 1 2 3

console.log(...{ a: 1, b: 2 });
// TypeError: Found non-callable @@iterator
```

Spread 문법을 사용해 배열을 함수의 인수로 전달하면 배열의 요소를 분해하여 순차적으로 매개변수에 할당한다. ES5에서는 일반적으로 **Function.prototype.apply**나 **Function.prototype.call**를 사용했다.

```jsx
// ES5
function foo(x, y, z) {
  console.log(x); // 1
  console.log(y); // 2
  console.log(z); // 3
}

const arr = [1, 2, 3];

foo.apply(null, arr);
// foo.call(null, 1, 2, 3);

// ES6
function foo(x, y, z) {
  console.log(x); // 1
  console.log(y); // 2
  console.log(z); // 3
}

const arr = [1, 2, 3];

foo(...arr);
```

Spread 문법은 배열에서 다양한 경우에 편리하게 사용될 수 있다.

1. concat (**concat**은 원본 배열을 변경하지 않는다.)

    ```jsx
    // ES5
    var arr = [1, 2, 3];
    console.log(arr.concat([4, 5, 6])); // [ 1, 2, 3, 4, 5, 6 ]

    // ES6
    const arr = [1, 2, 3];
    console.log([...arr, 4, 5, 6]); // [ 1, 2, 3, 4, 5, 6 ]
    ```

2. push (**push**는 성능면에서 좋지 않다. 위에 처럼 spread 문법을 사용하거나 **length** 프로퍼티를 이용해 직접 요소를 추가하는것이 좋다.)

    ```jsx
    // ES5
    var arr1 = [1, 2, 3];
    var arr2 = [4, 5, 6];

    // push를 사용해 배열을 인자로 넘기면, 배열 자체가 추가되므로 apply를 사용해야 한다.
    Array.prototype.push.apply(arr1, arr2);

    console.log(arr1); // [ 1, 2, 3, 4, 5, 6 ]

    // ES6
    const arr1 = [1, 2, 3];
    const arr2 = [4, 5, 6];

    // ...arr2는 [4, 5, 6]을 개별 요소로 분리한다
    arr1.push(...arr2); // == arr1.push(4, 5, 6);

    console.log(arr1); // [ 1, 2, 3, 4, 5, 6 ]
    ```

3. splice (배열의 특정 위치에 다른 배열의 요소 삽입)

    ```jsx
    // ES5
    var arr1 = [1, 2, 3, 6];
    var arr2 = [4, 5];

    // arr1.splice(3, 0, 4, 5) → arr1[3]부터 0개의 요소를 제거하고 그자리(arr1[3])에 새로운 요소(4, 5)를 추가한다.
    Array.prototype.splice.apply(arr1, [3, 0].concat(arr2));
    console.log(arr1); // [ 1, 2, 3, 4, 5, 6 ]

    // ES6
    const arr1 = [1, 2, 3, 6];
    const arr2 = [4, 5];

    // ...arr2는 [4, 5]을 개별 요소로 분리한다
    arr1.splice(3, 0, ...arr2); // == arr1.splice(3, 0, 4, 5);

    console.log(arr1); // [ 1, 2, 3, 4, 5, 6 ]
    ```

4. copy

    ```jsx
    // ES5
    var arr  = [1, 2, 3];
    var copy = arr.slice();

    console.log(copy); // [ 1, 2, 3 ]
    copy.push(4);
    console.log(copy); // [ 1, 2, 3, 4 ]
    console.log(arr);  // [ 1, 2, 3 ]

    // ES6
    const arr = [1, 2, 3];
    const copy = [...arr];
    console.log(copy); // [ 1, 2, 3 ]

    copy.push(4);
    console.log(copy); // [ 1, 2, 3, 4 ]
    console.log(arr);  // [ 1, 2, 3 ]
    ```

    **splice**와 spread ****문법은 모두 원본 배열의 각 요소를 **shallow copy**하여 새로운 복사본을 생성한다. **deep copy**를 위해서는 **lodash**를 사용한다.

5. 유사배열 객체를 배열로 변환

    ```jsx
    const htmlCollection = document.getElementsByTagName('li');
    const newArray = [...htmlCollection];
    ```


## 매개변수 기본값

자바스크립트에서 함수를 호출할 때 매개변수의 개수와 인수의 개수가 일치하는 않는 경우에도 에러가 발생하지 않는다. 다만 인수가 부족한 경우, 매개변수의 값은 **undefined**이다.

ES6에서는 매개변수 기본값을 사용해 매개변수에 초기 값을 설정할 수 있다. 인수를 전달하지 않은 경우 지정한 기본값으로 할당된다.

```jsx
// ES5
function sum(x, y) {
	x = x || 0;
	y = y || 0;
	return x + y;	
}

// ES6
function sum(x= 0, y = 0) {
	return x + y;
}
```

매개변수 기본값은 함수 객체의 **length** 프로퍼티와 **arguments** 객체에 영향을 주지 않는다.

```jsx
function foo(x, y = 0) {
  console.log(arguments);
}

console.log(foo.length); // 1

sum(1);    // Arguments { '0': 1 }
sum(1, 2); // Arguments { '0': 1, '1': 2 }
```

## Rest/Spread 프로퍼티

Rest/Spread 프로퍼티는 객체 리터럴을 분해하고 병합하는 편리한 기능을 제공한다. **Object.assign**을 대체할 수 있는 문법이다.

```jsx
// Spread 프로퍼티
const n = { x: 1, y: 2, ...{ a: 3, b: 4 } };
console.log(n); // { x: 1, y: 2, a: 3, b: 4 }

// Rest 프로퍼티
const { x, y, ...z } = n;
console.log(x, y, z); // 1 2 { a: 3, b: 4 }

// Object.assign
const merged = Object.assign({}, { x: 1, y: 2 }, { y: 10, z: 3 });
console.log(merged); // { x: 1, y: 10, z: 3 }

// Rest/Spread 프로퍼티
const merged = { ...{ x: 1, y: 2 }, ...{ y: 10, z: 3 } };
console.log(merged); // { x: 1, y: 10, z: 3 }
```

### Reference

---

[https://poiemaweb.com/es6-extended-parameter-handling](https://poiemaweb.com/es6-extended-parameter-handling)