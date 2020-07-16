이 글은 인프런 Node.js 교과서 강의를 보고 작성되었으며 전체 코드는 https://github.com/ZeroCho/nodejs-book에서 참고할 수 있다.

## Express

Express는 Node.js의 http 모듈과 미들웨어라 불리는 컴포넌트를 기반으로 하는 웹 프레임워크다. 미들웨어를 통해 http 요청 처리나 쿠키, 세션 관리, 라우팅 등의 작업을 더 간단하게 처리할 수 있다.

## express-generator

Express 기반으로 프로젝트를 시작할 수 있도록 기본적인 세팅과 파일들을 생성해주고 구조를 잡아주는 패키지

**설치**

```bash
$ npm i -g express-generator
```

**Express 앱 생성 & 종속 항목 설치**

```bash
$ express --view=pug myapp
$ cd myapp
$ npm install
```

view engine으로 **pug**를 사용하는 express앱을 생성한다.

생성된 앱은 다음과 같은 디렉토리 구조를 갖는다.

```
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug
```

**서버 실행**

```bash
$ npm start
```

package.json에서 scripts를 확인해보면 start 명령어에 `node ./bin/www`라고 설정된 것을 확인할 수 있다. **bin/www** 파일은 서버 실행부이며 서버의 핵심 로직은 **app.js**에 작성한다.  bin/www 파일의 기본적인 내용은 아래와 같다.

```jsx
var app = require('../app');
var http = require('http');

...
var port = normalizePort(process.env.PORT || '3000');
app.set('port', port);
var server = http.createServer(app);
server.listen(3000);

...생략
```

`app.set(key, value)`로  데이터를 저장할 수 있으며 나중에 `app.get(key)`로 데이터를 가져올 수 있다. app.js 이외의 파일에서도 데이터를 저장하고 공유하려면 `req.app.set()`, `req.app.get()`을 사용하면 된다.

app 모듈이 http.createServer 메소드의 콜백 함수 역할을 한다.

## app.js

아래 예제는 app.js의 기본적인 구조이다.

```jsx
const express = require('express');

const app = express();
app.get('/', (req, res) => {
	res.send('Hello World')
});
app.get('/user', (req, res) => {
	res.send('Hello users')
});
app.post('/', (req, res) => {
});
module.exports = app;
```

express 함수를 호출해 app 객체를 생성해주고, `app.get(path, handler)`과 같은 라우팅 메소드를 이용해 특정 http 메소드, url 경로에 대한 라우팅을 정의한다.

## 미들웨어 middleware

Express는 자체적인 최소한의 기능을 갖춘 라우팅 및 미들웨어 웹 프레임워크이며, Express 애플리케이션은 기본적으로 일련의 미들웨어 함수 호출이다.

미들웨어 함수는 요청 오브젝트(`req`), 응답 오브젝트 (`res`), 그리고 애플리케이션의 요청-응답 주기 중 그 다음의 미들웨어 함수 대한 액세스 권한을 갖는 함수이다. 그 다음의 미들웨어 함수는 일반적으로 `next`라는 이름의 변수로 표시된다. `app.use()` 함수를 이용해 미들웨어를 앱 인스턴스에 바인드한다.

### 커스텀 미들웨어

```jsx
app.use((req, res, next) => {
	console.log('미들웨어');
	next();
});

// 여러 개 입력 가능
app.use(function(req, res, next) {
  console.log('첫 번째 미들웨어');
  next();
}, function (req, res, next) {
  console.log('두 번째 미들웨어:');
  next();
});
```

미들웨어 함수가 요청-응답 주기를 종료하지 않는 경우에는 `next()`를 호출하여 그 다음 미들웨어 함수에 제어를 전달해야 한다. `next()`도 호출하지 않고 req 메소드도 사용하지 않으면 클라이언트가 timeout 될 때까지 계속 기다리게 된다.

### 기본 제공 미들웨어

**express.static**

Express 애플리케이션의 정적 리소스를 제공한다.

```jsx
// public 폴더를 정적 리소스의 루트 디렉토리로 지정
app.use(express.static(path.join(__dirname, 'public')));
// 여러개의 정적 디렉토리를 가질 수 있음
app.use(express.static(path.join(__dirname, 'files')));
```

app.js에서 미들웨어 바인딩부의 상단에 위치시켜 정적 리소스에 대한 요청 이후 추가적인 서버 동작을 피할 수 있다.

**express.json, express.urlencoded**

request body로부터 데이터를 편리하게 추출해서, req.body에 담는다.

```jsx
app.use(express.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.post('/profile', function(req, res) => {
  console.log(req.body)
})
```

### 유명 써드파티 미들웨어

```jsx
var cookieParser = require('cookie-parser');
var logger = require('morgan');
const session = require('exprsss-session');

app.use(logger('dev'));
app.use(cookieParser());
app.use(session({
	resave: false,
    saveUninitialized: false,
    secret: 'secret code',
    cookie: {
    	httpOnly: true,
        secure: false,
    },
}));
```

**morgan** : 요청에 대한 로그를 콘솔에 표시

**body-parser** : 4.16 버전 이후부터 express에 일부 내장되어 설치 없이 body 내용 확인 가능

**cookie-parser** : 클라이언트에서 전송된 쿠키 확인 가능

**express-session** : 세션 관리용 미들웨어

**flash** : 일회성 메세지 작성

### 라우터 미들웨어

express.Router()로 라우팅 기능을 구현할 수 있다.

```jsx
// index.js
var express = require('express');
var router = express.Router();

// /
router.get('/', function(req, res, next) {
  console.log('view engine', req.app.get('view engine'));
  res.render('index', { title: 'Express' });
});
// /express
router.get('/express', function(req, res, next) {
  res.send('Hello express');
});

module.exports = router;
```

```jsx
// users.js
var express = require('express');
var router = express.Router();

// /users
router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});
// /users/list
router.post('/list', function(req, res, next) {
  res.send('respond with a resource');
});

module.exports = router;
```

```jsx
// app.js
var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');

app.use('/', indexRouter);
app.use('/users', usersRouter);
```

**응답 메소드**

- res.send() : 다양한 유형의 응답을 전송
- res.sendFile() : 파일 전송
- res.json() : json 응답을 전송
- res.sendStatus() : 응답 상태 코드 전송(데이터 전송 불가)
- res.status() : 응답 상태 코드와 함께 데이터 전송가능
- res.end() : 응답 프로세스를 종료
- res.render() : view template을 렌더링

### 에러처리 미들웨어

앞선 미들웨어에서 요청이 처리되지 못한 경우 404 error를 발생시킨다.

```jsx
// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});
```

서버 내부의 문제로 에러가 발생한 경우 500 error를 발생시킨다.

```jsx
// index.js
// 다른 파일에서 에러가 발생한 경우
const express = require('express');
const router = express.Router();

router.get('/', (req, res, next) => {
	try{
		throw new Error('Server error');
	} catch(error) {
		// next(error)를 하면 다음 미들웨어를 건너뛰고 바로 에러처리 미들웨어로 이동한다.
		next(error);
	}
});
```

```jsx
// app.js
// error handler
app.use(function(err, req, res, next) {
  console.log(err); // Server error
	
	// res.locals에 데이터를 저장하면 template engine의 변수로 사용할 수 있다.
	res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});
```

## Template engine

Express는 pug, ejs와 같은 템플릿 엔진을 사용할 수 있다.

### pug

```jsx
// 템플릿은 views 디렉터리에 작성한다.
app.set('views', __dirname + '/views');
app.set('view engine', 'pug');

app.get('/', (req, res) => {
  res.render('home', { body: 'Hello world' })
});
```

### ejs

```jsx
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.engine('html', require('ejs').renderFile);

app.get('/', (req, res) => {
  res.render('home', { body: 'Hello world'})
});
```

ejs는 레이아웃 문법을 지원하지 않아 nunjucks을 더 추천한다.