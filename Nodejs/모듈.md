## 모듈

Node.js에서 module은 파일과 1대1의 대응 관계를 가지며 하나의 모듈은 자신만의 독립적인 실행 영역(Scope)를 가진다.

모듈은 module.exports 또는 exports 객체를 통해 정의하고 외부로 공개한다. 그리고 공개된 모듈은 require 함수를 사용하여 임포트한다.

## exports

모듈 안에 선언한 모든 것들은 기본적으로 해당 모듈 내부에서만 참조 가능하다. 모듈 안에 선언한 항목을 외부에서 사용하도록 하려면 **exports** 객체를 사용해야 한다.

외부에 공개할 대상을 exports 객체의 프로퍼티 또는 메소드로 정의한다.

```jsx
// circle.js
const { PI } = Math;

exports.area = (r) => PI * r * r;
exports.circumference = (r) => 2 * PI * r;
```

```jsx
// app.js
const circle = require('./circle.js'); // == require('./circle')

console.log(circle.area(4));
console.log(circle.circumference(4));
```

## **module.exports**

exports 객체는 프로퍼티 또는 메소드를 여러 개 정의할 수 있다. 하지만 **module.exports**에는 하나의 값(원시 타입, 함수, 객체)을 할당할 수 있다.

require는 module.exports에 할당한 값 자체, 즉 객체를 반환한다.

```jsx
// circle.js
const { PI } = Math;

module.exports = function (r) {
  return {
    area() { return PI * r * r; },
    circumference() { return 2 * PI * r}
  };
}

// app.js
const circle = require('./circle');
const myCircle = circle(4);

console.log(myCircle.area);
console.log(myCircle.circumference);
```

```jsx
// circle.js
const { PI } = Math;

function area(r) {
    return PI * r * r;
}

function circumference(r) {
    return 2 * PI * r;
}

// 객체리터럴 사용
module.exports = { area, circumference };

// app.js
// 비구조화 할당 사용
const { area, circumference } = require('./circle');

console.log(area(4));
console.log(circumference(4));
```

## module.exports, exports 차이

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/254c9a62-640e-4a80-b92c-b2a5c3da52f6/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200706%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200706T134829Z&X-Amz-Expires=86400&X-Amz-Signature=84c122a30c9a5bb04c039225cd2cef36a29ed5ab0af640d45d17f1e468009c7a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

## require

require의 인수로 파일과 디렉토리를 지정할 수 있다. require할 때 파일 확장자를 생략할 수 있다.

디렉토리를 인수로 넘긴 경우는 해당 디렉토리의 index.js를 로드한다.

```plaintext
project/
├── app.js
└── module/
    ├── index.js
    ├── calc.js
    └── print.js
```


```jsx
const myModule = require('./module');
```

위와 같은 디렉토리 구조에서 module 디렉토리를 require 함수로 호출하면 index.js를 로드한다. 이때 로드되는 index.js 내에서 calc.js과 print.js를 require하면 한번의 require로 alc.js과 print.js의 모든 기능을 사용할 수 있다.

```jsx
// module/index.js
module.exports = {
  calc: require('./calc'),
  print: require('./print')
};
```

## 코어 모듈 (내장 모듈)

Node.js에서 기본으로 포함하고 있는 코어 모듈을 로딩할 때는 경로를 명시하지 않아도 된다. npm을 통해 설치한 외부 패키지 또한 마찬가지이다.

```jsx
const http = require('http');
const mongoose = require('mongoose');
```

### Reference

[https://poiemaweb.com/nodejs-module](https://poiemaweb.com/nodejs-module)