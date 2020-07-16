이 글은 인프런 Node.js 교과서 강의를 보고 작성되었으며 전체 코드는 [https://github.com/ZeroCho/nodejs-book](https://github.com/ZeroCho/nodejs-book)에서 참고할 수 있다.

## MongoDB

MongoDB는 문서지향적 NoSQL 데이터베이스로 전통적인 테이블-관계 기반의 RDBMS의 한계를 극복하기 위한 새로운 형태의 데이터베이스이다.

MongoDB의 가장 기본적인 데이터를 Document라 부르는데 이는 RDBMS의 row에 해당된다. Document의 집합인 Collection은 Table에 대응되며, Collection의 집합은 DB로 RDBMS와 동일하다.

MongoDB는 MySQL의 테이블과 같은 스키마가 고정된 구조 대신 BSON(Binary JSON)이라 불리는 동적 스키마형 문서를 사용하여 같은 Collection 내에서도 다른 Schema의 document를 가질 수 있다.

또한 배열이나 날짜 등 기존 RDBMS에서 지원하지 않는 형태로도 저장할 수 있어 JOIN없이 한 문서에 좀 더 이해하기 쉬운 형태로 정보를 저장할 수 있으며 CRUD Query가 고속으로 동작한다.

**Document**

JSON object 형태의 key-value 쌍으로 이루어진 데이터 구조로 구성된다. value에는 다른 document, array, document array가 포함될 수 있다.

각 Document는 _id라는 고유한 값을 가지며 ObjectId 타입이다.

```jsx
{
	_id: ObjectId("5099803df3f4948bd2f98391"),
	name: "joy",
	age: 26,
	groups: ["news", "sports"],
	birth: new Date('October 17, 1994')
}
```

### 설치

**설치 및 실행**

[MongDB](https://www.mongodb.com/try#community) 사이트에서 Community Server를 다운받아 설치한다.

설치가 완료되면 데이터가 저장될 기본 데이터베이스 디렉토리(C:\data\db)를 생성한다.

```bash
$ mkdir C:\data\db
```

디렉토리 생성이 되었다면, MongoDB가 설치된 디렉토리로 이동해 서버를 실행한다.

```bash
$ cd C:\Program Files\MongoDB\Server\4.2\bin
$ mongod
```

다른 경로에 기본 데이터베이스를 생성하였다면 경로를 설정해주어야 한다.

```bash
$ mongod --dbpath C:\Program Files\MongoDB\data\db
```

서버가 기동되었다면 터미널에 mongo 명령어를 사용해 데이터베이스에 접속하고 조회할 수 있다. GUI 기반의 MongoDB 클라이언트인 MongoDB Compass를 사용할수도 있다.

```bash
$ mongo
```

**인증 추가**

일반적인 RDBMS와 달리 MongoDB 서버는 기본적으로 보안 모델없이 실행된다. 따라서 별도로 서버에 인증 과정을 추가해주어야 한다.

우선 보안 모델이 없는 MongoDB 서버를 실행해 admin 데이터베이스에 관리자 계정을 생성한다.

```bash
$ mongod
> use admin
> db.createUser({
    user: 'root',
    pwd: 'password',
    roles: ['userAdminAnyDatabase']
})
```

관리자 계정 생성 후, 보안 모델을 가진 서버로 재 실행한다.

```bash
$ mongod --auth
```

두가지 방법으로 데이터베이스에 관리자를 인증할 수 있다.

```bash
$ mongo
> use admin
> db.auth('username', 'password')
1
```

첫 번째 방법은 쉘에 접속해 db.auth()를 사용하는 것이다. 1이 나오면 성공이다. 두 번째 방법은 술에 접속하는 것과 동시에 인증을 하는 것이다.

```bash
$ mongo -u username -p password --authenticationDatabasee admin
```

### Mongoose

Mongoose는 MongoDB의 ODM으로 DB에서 document를 조회할 때 자바스크립트 객체로 바꿔주는 역할을 한다.

MongoDB는 고정 스키마가 존재하지 않아서 자유도가 높지만 명시적인 구조가 없어 어떤 필드가 어떤 데이터 타입인지 알기 어렵다는 단점이있다. 이러한 문제를 보완하기 위해 몽구스는 스키마를 사용해 데이터베이스에 저장되는 document의 구조를 JSON 형태로 정의해서 document를 저장할 때 타입과 필드를 검사한다.

또한 몽구스는 프로미스와 콜백의 사용이 가능하고, 쿼리 빌더로 간단하게 쿼리를 사용할 수 있으며,  populate를 이용해 JOIN과 비슷한 기능을 흉내낼 수 있다는 장점이 있다.

**설치**

몽구스는 npm으로 설치한다.

```bash
$ npm i mongoose
```

### 스키마 생성

몽구스에서 스키마는 sequelize의 model과 비슷한 역할을 한다.

루트 디렉토리에 **schemas** 폴더를 생성하고 그 안에 **index.js**와 스키마 생성을 위한 파일들을 생성한다. 이 글에서는 사용자와 댓글 관련 데이터 스키마의 생성을 위해 user.js, comment.js를 만든다.

```jsx
// schemas/index.js
const mongoose = require('mongoose');

const connect = () => {
  if (process.env.NODE_ENV !== 'production') {
    mongoose.set('debug', true);
  }
  mongoose.connect('mongodb://root:password@localhost:27017/admin', {
    dbName: 'nodejs',
    useNewUrlParser: true,
    useCreateIndex: true,
  }, (error) => {
    if (error) {
      console.log('몽고디비 연결 에러', error);
    } else {
      console.log('몽고디비 연결 성공');
    }
  });
};

mongoose.connection.on('error', (error) => {
  console.error('몽고디비 연결 에러', error);
});
mongoose.connection.on('disconnected', () => {
  console.error('몽고디비 연결이 끊겼습니다. 연결을 재시도합니다.');
  connect();
});

module.exports = connect;
```

index.js에서는 몽구스를 통한 데이터베이스 서버 연결을 위한 코드를 작성한다. 연결에 필요한 값을 설정하고 에러 및 연결이 끊기는 이벤트 처리를 위한 핸들러를 만들어준다.

```jsx
// schemas/index.js
const mongoose = require('mongoose');

const { Schema } = mongoose;
const userSchema = new Schema({
  name: {
    type: String,
    required: true,
    unique: true,
  },
  age: {
    type: Number,
    required: true,
  },
  married: {
    type: Boolean,
    required: true,
  },
  comment: String,
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

module.exports = mongoose.model('User', userSchema);
```

```jsx
// schemas/comment.js
const mongoose = require('mongoose');

const { Schema } = mongoose;
const { Types: { ObjectId } } = Schema;
const commentSchema = new Schema({
  author: {
    type: ObjectId,
    required: true,
    ref: 'User',  // User의 _id 값을 참조한다.
  },
  comment: {
    type: String,
    required: true,
  },
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

module.exports = mongoose.model('Comment', commentSchema);
```

model() 메소드에 collection의 단수형 이름과 스키마를 인자로 주어 model을 생성한다. model은 생성자로 instance를 생성할 수 있는데 이는 개별 document를 나타낸다.

스키마와 관련된 파일을 모두 작성한 뒤 app.js에 연결해서 mongoose를 실행시킨다.

```jsx
// app.js
const connect = require('./schemas');

connect();
```

### 쿼리 수행

**routes** 폴더 아래 클라이언트의 요청을 처리할 수 있도록 라우터를 생성한다.

```jsx
// routes/index.js
const express = require('express');
const User = require('../schemas/user');

const router = express.Router();

router.get('/', async (req, res, next) => {
  try {
    const users = await User.find({});
    res.render('mongoose', { users });
  } catch (err) {
    console.error(err);
    next(err);
  }
});

module.exports = router;
```

```jsx
// routes/users.js
const express = require('express');
const User = require('../schemas/user');
const Comment = require('../schemas/comment');

const router = express.Router();

router.route('/')
  .get(async (req, res, next) => {
    try {
      const users = await User.find({});
      res.json(users);
    } catch (err) {
      console.error(err);
      next(err);
    }
  })
  .post(async (req, res, next) => {
    try {
      const user = await User.create({
        name: req.body.name,
        age: req.body.age,
        married: req.body.married,
      });
      console.log(user);
      res.status(201).json(user);
    } catch (err) {
      console.error(err);
      next(err);
    }
  });

router.get('/:id/comments', async (req, res, next) => {
  try {
    const comments = await Comment.find({ author: req.params.id })
      .populate('author');
    console.log(comments);
    res.json(comments);
  } catch (err) {
    console.error(err);
    next(err);
  }
});

module.exports = router;
```

```jsx
// routes/comments.js
const express = require('express');
const Comment = require('../schemas/comment');

const router = express.Router();

router.post('/', async (req, res, next) => {
  try {
    const comment = await Comment.create({
      author: req.body.id,
      comment: req.body.comment,
    });
    console.log(comment);
    const result = await Comment.populate(comment, { path: 'author' });
    res.status(201).json(result);
  } catch (err) {
    console.error(err);
    next(err);
  }
});

router.route('/:id')
  .patch(async (req, res, next) => {
    try {
      const result = await Comment.update({
        _id: req.params.id,
      }, {
        comment: req.body.comment,
      });
      res.json(result);
    } catch (err) {
      console.error(err);
      next(err);
    }
  })
  .delete(async (req, res, next) => {
    try {
      const result = await Comment.remove({ _id: req.params.id });
      res.json(result);
    } catch (err) {
      console.error(err);
      next(err);
    }
  });

module.exports = router;
```

```jsx
// app.js
const indexRouter = require('./routes');
const usersRouter = require('./routes/users');
const commentsRouter = require('./routes/comments');

app.use('/', indexRouter);
app.use('/users', usersRouter);
app.use('/comments', commentsRouter);
```

**CRUD**

- CREATE : Model.create({key: value})
- READ : Model.find(), Model.find({key: value})
- UPDATE : Model.update({key: value}, {key: value}) 조건이 먼저, 수정을 위한 값을 뒤에
- DELETE : Model.destroy({key: value})

### Mongoose populate

populate를 이용해 sequelize의 include처럼 JOIN 기능을 흉내낼 수 있다. 하지만 include와 다르게 실제 JOIN이 아니라 몽구스에서 따로 구현한 기능이기 때문에 성능이 떨어진다.

```jsx
// routes/user.js
router.get('/:id/comments', async (req, res, next) => {
  try {
    const comments = await Comment.find({ author: req.params.id })
      .populate('author');
    console.log(comments);
    res.json(comments);
  } catch (err) {
    console.error(err);
    next(err);
  }
});
```

### Reference

[https://edu.goorm.io/learn/lecture/557/한-눈에-끝내는-node-js/lesson/174384/mongodb란](https://edu.goorm.io/learn/lecture/557/%ED%95%9C-%EB%88%88%EC%97%90-%EB%81%9D%EB%82%B4%EB%8A%94-node-js/lesson/174384/mongodb%EB%9E%80)

[https://namu.wiki/w/MongoDB](https://namu.wiki/w/MongoDB)

[https://planbs.tistory.com/entry/MongoDB-인증authorization-추가하기](https://planbs.tistory.com/entry/MongoDB-%EC%9D%B8%EC%A6%9Dauthorization-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0)