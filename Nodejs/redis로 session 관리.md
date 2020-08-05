## Session & Cookie

http의 stateless한 성질때문에 동일한 클라이언트가 서버에 반복해서 접속해도 로그인 상태를 유지할 수 없다. 이러한 문제를 보완하기 위해 Session과 Cookie를 통해 사용자 정보를 유지한다. 하지만 session을 서버 메모리에 직접 저장한 경우 서버가 재실행 시 세션 정보가 날아가고, 복수의 서버가 세션 정보를 공유할 수 없다는 단점이 있다.

## Redis

Redis는 Key-Value 구조의 비관계형 데이터를 저장하고 관리하기 위한 NoSQL의 일종으로 모든 데이터를 메모리로 불러와서 처리하는 메모리 기반 DBMS이다. 메모리에서 데이터를 처리하기 때문에 성능이 좋다. 자료구조 형식으로 데이터를 관리하는데, 리스트, 집합, 정렬된 집합, 해쉬 데이터 모델을 지원한다.

Redis를 사용해 세션을 관리하면 서버가 종료되어도 세션 정보가 보존되고, 복수의 서버가 세션을 공유할 수 있게 된다.

## Express에서 Redis를 사용해 세션 관리하기

### Redis 서버 설치, 실행

[https://github.com/microsoftarchive/redis/releases](https://github.com/microsoftarchive/redis/releases)

위 주소에서 msi 파일을 받아 설치한다. 설치가 완료되면 Redis 서버가 실행된다.

Redis 설치 디렉토리에 있는 redis-cli.exe를 실행한다.

### Express와 연동

Redis 실행에 필요한 패키지를 설치한다.

```bash
npm install express-session redis
npm install connect-redis
```

app.js

```jsx
const redis = require('redis');
const RedisStore = require('connect-redis')(session);

const client = redis.createClient({
  host: process.env.REDIS_HOST,
  port: process.env.REDIS_PORT,
  logErrors: true
});

const sessionOption = {
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
      httpOnly: true,
      secure: false
  },
  store: new RedisStore({ client })
};
app.use(session(sessionOption));
```

.env 파일에 위에서 실행한 redis 서버에 대한 HOST, PORT 정보를 저장해서 redis 클라이언트를 생성할 때 해당 값을 불러온다.