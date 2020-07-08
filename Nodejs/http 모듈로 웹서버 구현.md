Node.js 교과서 강의내용 정리

## http 모듈

http 웹 서버를 생성하는 것과 관련된 기능을 담당하는 모듈

```jsx
const http = require('http');

// 서버에 요청이 들어오면 콜백함수 실행
http.createServer((req, res) => {
    console.log('서버 실행');
    res.writeHead(200, {'content-Type': 'text/html; charset=utf-8'});
    res.write('<h1>Hello Node!</h1>');
    res.write('<p>안녕하세요</p>');
    res.end('<p>http 서버입니다.</p>');
}).listen(8080, () => {
    console.log('8080번 포트에서 대기 중입니다.')
});
```

- createServer() : Server 객체 생성
- req : 요청에 대한 정보를 담은 객체
- res : 응답에 대한 정보를 담는 객체
    - writeHead(statusCode, object) : 응답 헤더 작성
    - wrtie(), end() : 응답 본문 작성
- Server.listen(port, hostname, callback) : 서버 실행. http의 기본 포트는 80, https는 443으로 기본 포트는 생략이 가능하다. 1024번 아래의 포트는 다른 프로그램이 사용 중일 확률이 높고, 리눅스나 맥에서는 관리자 권한이 필요하다.
- Server.close() : 서버 종료

## 응답으로 파일 읽어 보내기

```java
const http = require('http');
const fs = require('fs');
const server = http.createServer((req, res) => {
  console.log('서버 실행');
	fs.readFile('./index.html', (err, data) => {
      if(err) {
          throw err;
      }
      res.end(data);
  });
}).listen(8080);

server.on('listening', () => {
  console.log('8080번 포트에서 대기 중입니다.')
});

server.on('error', (error) => {
	console.error(error);
});
```

`res.end(data)`로 응답해줄때, `data.toString()`으로 변환시켜줄 필요가 없다.

## 쿠키 설정

```java
const http = require('http');

// 쿠키를 파싱하기 위한 함수 선언
const parseCookies = (cookie = '') =>
  cookie
    .split(';')
    .map(v => v.split('='))
    .map(([k, ...vs]) => [k, vs.join('=')])
    .reduce((acc, [k, v]) => {
        acc[k.trim()] = decodeURIComponent(v);
        return acc;
    }, {});

const server = http.createServer((req, res) => {
	// 클라이언트에서 서버로 전달
	console.log(parseCookies(req.url, req.headers.cookie));
	// 서버에서 클라이언트로 전달
  res.writeHead(200, { 'Set-Cookie': 'mycookie=test' });
	res.end('Hello Cookie');
}).listen(8080);

server.on('listening', () => {
  console.log('8080번 포트에서 대기 중입니다.')
});

server.on('error', (error) => {
	console.error(error);
});
```

- 저장된 쿠키는 브라우저 개발자 도구에서 **Application** - **Cookies**에서 확인가능
- **req.url**로 클라이언트에서 어느 url로 요청했는지 알 수 있음

## 라우터 분기 처리와 쿠키

```java
const http = require('http');
const fs = require('fs');
const url = require('url');
const qs = require('querystring');

// 쿠키를 파싱하기 위한 함수 선언
const parseCookies = (cookie = '') =>
  cookie
    .split(';')
    .map(v => v.split('='))
    .map(([k, ...vs]) => [k, vs.join('=')])
    .reduce((acc, [k, v]) => {
        acc[k.trim()] = decodeURIComponent(v);
        return acc;
    }, {});

const server = http.createServer((req, res) => {
  const cookies = parseCookies(req.headers.cookie);
	if(req.url.startsWith('/login')) {
    const { query } = url.parse(req.url);
    const { name } = qs.parse(query);
    const expires = new Date();
    expires.setMinutes(expires.getMinutes() + 5);
    res.writeHead(302, {
        Location: '/',
        'Set-Cookie': `name=${encodeURIComponent(name)}; Expires=${expires.toGMTString()}` });
    res.end('Hello', name);
  } else if(cookies.name) {
    res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8'});
    res.end(`${cookies.name}님 안녕하세요`)
  } else {
    fs.readFile('./index.html', (err, data) => {
      res.end(data);
    })
  }
}).listen(8080);

server.on('listening', () => {
  console.log('8080번 포트에서 대기 중입니다.')
});

server.on('error', (error) => {
	console.error(error);
});
```

- **req.url**로 요청이 들어온 url를 파악해 라우팅할 수 있음
- **form** 태그로 전송받은 로그인 정보를 쿠키로 만들어 클라이언트로 전송해 쿠키가 있는 경우 로그인 상태 유지
- **상태 코드** **302**는 임시 이동으로, 브라우저에게 Location에 적힌 페이지로 이동하라는 뜻
- 헤더에 쿠키 설정할 때 쿠키의 유효기간도 설정할 수 있음

## 메모리 세션 구현

```java
const http = require('http');
const fs = require('fs');
const url = require('url');
const qs = require('querystring');

const parseCookies = (cookie = '') =>
  cookie
    .split(';')
    .map(v => v.split('='))
    .map(([k, ...vs]) => [k, vs.join('=')])
    .reduce((acc, [k, v]) => {
        acc[k.trim()] = decodeURIComponent(v);
        return acc;
    }, {});

const session = {};

const server = http.createServer((req, res) => {
    const cookies = parseCookies(req.headers.cookie);
	if(req.url.startsWith('/login')) {
        const { query } = url.parse(req.url);
        const { name } = qs.parse(query);
        const randomInt = +new Date();
        const expires = new Date();
        expires.setMinutes(expires.getMinutes() + 5);
        session[randomInt] = {
            name,
            expires
        };
        res.writeHead(302, {
            Location: '/',
            'Set-Cookie': `session=${randomInt}; Expires=${expires.toGMTString()}` });
        res.end('Hello', name);
    } else if(cookies.session && session[cookies.session] && session[cookies.session].expires > new Date()) {
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8'});
        res.end(`${session[cookies.session].name}님 안녕하세요`)
    } else {
        fs.readFile('./index.html', (err, data) => {
            res.end(data);
        })
    }
}).listen(8080);

server.on('listening', () => {
  console.log('8080번 포트에서 대기 중입니다.')
});

server.on('error', (error) => {
	console.error(error);
});
```

- 중요한 정보가 쿠키에 그대로 저장될 경우 유출될 위험이 있다.
- 세션은 브라우저가 아닌 서버측에 데이터를 저장하고, 세션 아이디를 생성해 브라우저에게 전송하는 식으로 데이터를 관리한다.
- 위 예제에서 세션 아이디를 생성, 저장하는 방식은 세션에 대한 이해를 돕기 위한 것일 뿐, 실무에서는 이런식으로 하지 않는다. 또한 지금 구조의 세션도 브라우저의 쿠키가 노출되면 서버 정보가 유출될 수 있다.
- `+new Date()`는 현재 시간을 millisecond로 가져옴

## REST API

```jsx
const http = require('http');
const fs = require('fs');

const users = {};

http.createServer((req, res) => {
  if (req.method === 'GET') {
    if (req.url === '/') {
      return fs.readFile('./restFront.html', (err, data) => {
        if (err) {
          throw err;
        }
        res.end(data);
      });
    } else if (req.url === '/about') {
      return fs.readFile('./about.html', (err, data) => {
        if (err) {
          throw err;
        }
        res.end(data);
      });
    } else if (req.url === '/users') {
      return res.end(JSON.stringify(users));
    }
    return fs.readFile(`.${req.url}`, (err, data) => {
      if (err) {
        res.writeHead(404, 'NOT FOUND');
        return res.end('NOT FOUND');
      }
      return res.end(data);
    });
  } else if (req.method === 'POST') {
    if (req.url === '/users') {
      let body = '';
      req.on('data', (data) => {
        body += data;
      });
      return req.on('end', () => {
        console.log('POST 본문(Body):', body);
        const { name } = JSON.parse(body);
        const id = Date.now();
        users[id] = name;
        res.writeHead(201);
        res.end('등록 성공');
      });
    }
  } else if (req.method === 'PUT') {
    if (req.url.startsWith('/users/')) {
      const key = req.url.split('/')[2];
      let body = '';
      req.on('data', (data) => {
        body += data;
      });
      return req.on('end', () => {
        console.log('PUT 본문(Body):', body);
        users[key] = JSON.parse(body).name;
        return res.end(JSON.stringify(users));
      });
    }
  } else if (req.method === 'DELETE') {
    if (req.url.startsWith('/users/')) {
      const key = req.url.split('/')[2];
      delete users[key];
      return res.end(JSON.stringify(users));
    }
  }
  res.writeHead(404, 'NOT FOUND');
  return res.end('NOT FOUND');
})
  .listen(8085, () => {
    console.log('8085번 포트에서 서버 대기중입니다');
  });
```

- REST API이기 때문에 먼저 HTTP method를 가지고 분기 처리를 한다.
- 이후 method 별로 url에 따라 분기 처리를 한다.
- 요청 본문은 `req.on('data', () => {})`와 `req.on('end', () => {})`를 통해 받아와 처리한다. 데이터가 스트림으로 전송되는 동안 `req.on('data', () => {})` 실행되고, 전송이 완료되면 `req.on('end', () => {})`이 실행된다.

위 예제에서 if문으로 복잡하게 라우터 처리가 된 부분을 아래와 같이 리팩토링 할 수 있다.

```jsx
const http = require('http');
const fs = require('fs');

const users = {};

// 라우터를 JSON 형식으로 정의
const router = {
  get: {
    '/': (req, res) => {
      fs.readFile('./restFront.html', (err, data) => {
        if (err) {
          throw err;
        }
        res.end(data);
      });
    },
    '/users': (req, res) => {
      res.end(JSON.stringify(users));
    },
    '*': (req, res) => {
      fs.readFile(`.${req.url}`, (err, data) => {
        if (err) {
          res.writeHead(404, 'NOT FOUND');
          return res.end('NOT FOUND');
        }
        return res.end(data);
      });
    },
  },
  post: {
    '/users': (req, res) => {
      let body = '';
      req.on('data', (data) => {
        body += data;
      });
      return req.on('end', () => {
        console.log('POST 본문(Body):', body);
        const { name } = JSON.parse(body);
        const id = Date.now();
        users[id] = name;
        res.writeHead(201);
        res.end('등록 성공');
      });
    },
  },
  put: {
    '/users': (req, res) => {
      const key = req.url.split('/')[2];
      let body = '';
      req.on('data', (data) => {
        body += data;
      });
      return req.on('end', () => {
        console.log('PUT 본문(Body):', body);
        users[key] = JSON.parse(body).name;
        return res.end(JSON.stringify(users));
      });
    },
  },
  patch: {

  },
  delete: {
    '/users': (req, res) => {
      const key = req.url.split('/')[2];
      delete users[key];
      return res.end(JSON.stringify(users));
    }
  }
};

http.createServer((req, res) => {
  const matchedUrl = router[req.method.toLowerCase()]['/' + req.url.split('/')[1]];
  (matchedUrl || router[req.method.toLowerCase()]['*'])(req, res);
})
.listen(8085, () => {
  console.log('8085번 포트에서 서버 대기중입니다');
});
```

## https, http2

```jsx
const https = require('https');

https.createServer({
	// 인증서 관련 옵션을 설정해주어야 함
	cert: fs.readFileSync('도메인 인증서 경로'),
	key: fs.readFileSync('도메인 비밀키 경로'),
	ca: [
		fs.readFileSync('상위 인증서 경로'),
		fs.readFileSync('상위 인증서 경로'),
	]
}, (req, res) => {
	res.end('https server');
}).listen(443);
```

```jsx
const http2 = require('http2');

http2.createSecureServer({
	cert: fs.readFileSync('도메인 인증서 경로'),
	key: fs.readFileSync('도메인 비밀키 경로'),
	ca: [
		fs.readFileSync('상위 인증서 경로'),
		fs.readFileSync('상위 인증서 경로'),
	]
}, (req, res) => {
	res.end('https server');
}).listen(443);
```

- http2는 https 기반으로 동작하므로 인증서가 필요하다.
- express와 호환 문제가 있어 spdy를 대신 사용한다.
- openssl 등에서 인증서를 발급받아 사용한다.

## Cluster로 멀티 프로세싱

싱글스레드 기반의 노드 서버의 성능을 높이기 위해 **cluster**를 이용해 멀티 프로세싱을 구현할 수 있다.

```jsx
const http = require('http');
const cluster = require('cluster');
const os = require('os');
const numCPUs = os.cpus().length;

if(cluster.isMaster){
    console.log('마스터 프로세스 아이디', process.pid);
    for(let i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
    // 워커 프로세스가 종료되면 이벤트 발생
    cluster.on('exit', (worker, code, signal) => {
        console.log(worker.process.pid, '워커 종료');
        cluster.fork();
    })
} else {
    http.createServer((req, res) => {
        res.end('http server');
    }).listen(8080);
    console.log(process.pid, '워커 실행');
}
```

- **cluster**에는 **master** 프로세스와 **worker** 프로세스가 있다. master 프로세스는 worker 프로세스를 관리하며, `cluster.fork()`로 worker를 생성한다.