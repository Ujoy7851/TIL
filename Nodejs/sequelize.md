## Sequelize

Sequelize는 Postgres, MySQL, MariaDB, SQLite, Microsoft SQL Server를 지원하는 Promise 패턴 기반의 Node.js ORM이다.

```bash
# 설치
$ npm i sequelize mysql2
$ npm i -g sequelize-cli

# sequelize와 관련 파일 및 폴더 생성 (config.json, models ..)
$ sequelize init
```

**참고: ORM이란 DBMS의 데이터를 객체 형태로 자동으로 매핑해주는 것을 말한다. ORM을 통해 객체 간의 관계를 바탕으로 SQL을 자동으로 생성하여 간접적으로 데이터베이스의 데이터를 다룰 수 있다.

**config.json**

DB 연동에 필요한 값을 저장

**models/index.js**

```jsx
const path = require('path');
const Sequelize = require('sequelize');

const env = process.env.NODE_ENV || 'development';
const config = require('../config/config.json')[env];

const sequelize = new Sequelize(config.database, config.username, config.password, config);

const db = {};
db.Sequelize = Sequelize;
db.sequelize = sequelize;

module.exports = db;
```

DB에 연결하기 위해서 config.json에 있는 DB 설정값으로 Sequelize 객체를 생성한다. db 객체에 넣어주고 다른 곳에서도 같은 Sequelize 객체를 사용할 수 있게 모듈화한다.

### DB 스키마 생성

```bash
$ sequelize db:create
```

위 명령어를 통해 config.js에 설정한대로 DB 스키마를 생성한다.

### 모델 정의

데이터베이스가 생성되었다면 데이터를 저장할 테이블을 만들어야 한다. sequelize에서 테이블은 **모델**을 통해 표현된다.그리고 이 모델 클래스의 인스턴스는 데이터베이스의 행을 나타낸다.

모델을 만들기 위해 **models** 폴더 안에 모델을 정의하는 파일들을 생성한다. 모델에 대한 정의는 **define** 메소드를 이용한다.

```jsx
// models/user.js
module.exports = (sequelize, DataTypes) => {
    return sequelize.define('user', {
        name: {
            type: DataTypes.STRING(20),
            allowNull: false,
            unique: true,
        },
        age: {
            type: DataTypes.INTEGER.UNSIGNED,
            allowNull: false,
        },
        married: {
            type: DataTypes.BOOLEAN,
            allowNull: false,
        },
        comment: {
            type: DataTypes.TEXT,
            allowNull: true,
        },
        createdAt: {
            type: DataTypes.DATE,
            allowNull: false,
            defaultValue: sequelize.literal('now()')
        },
    }, {
        timestamps: false,
    });
};
```

```jsx
// models/comment.js
module.exports = (sequelize, DataTypes) => {
    return sequelize.define('comment', {
        comment: {
            type: DataTypes.STRING(100),
            allowNull: false,
        },
        createAt: {
            type: DataTypes.DATE,
            allowNull: false,
            defaultValue: sequelize.literal('now()')
        },
    }, {
        timestamps: false,
    });
};
```

```jsx
// models/index.js
//... 생략

db.User = require('./user')(sequelize, Sequelize);
db.Comment = require('./comment')(sequelize, Sequelize);

//... 생략
```

define()의 첫 번째 인자로 모델명을, 두 번째 인자로 컬럼에 대한 정보를 전달한다. id와 createdAt, updateAt 컬럼은 기본으로 자동 생성된다. 컬럼에 대한 정보는 몇 가지 옵션을 이용해 전달한다.

**컬럼 옵션**

- type : 자료형
- allowNull : null 값 허용 여부
- defaultValue : 기본값
- unique : 고유값 여부
- comment : 컬럼 설명
- primaryKey : 기본키 여부 (id 대체)
- autoIncrement : 자동 증가열 여부. 기본값은 false
- freezeTableName: sequelize에서 모델명은 단수로, 테이블명은 복수로 다룬다. 모델명이 user인 경우 테이블명은 users로 생성하고 조회한다. freezeTableName은 테이블명을 모델명 그대로 사용하도록 해준다.

**컬럼 자료형**

- STRING
- INTEGER
- FLOAT
- TEXT
- DATE
- BOOLEAN

**config**

- timestamps : createAt과 updatedAt을 추가할 지 여부. 기본값은 true
- underscored : 컬럼 이름을 camelCase가 아닌 underscore 방식으로 사용

### 관계 설정

- 1:1 - hasOne, belongsTo
- 1:n - hasMany, belongsTo
- n:m - belongsToMany

위에서 정의된 사용자와 댓글의 모델에서 사용자는 다수의 댓글을 작성할 수 있으므로 서로 1:n 관계를 갖는다. 1:n 관계는 다음과 같이 hasMany와 belongsTo를 이용해 설정할 수 있다.

```jsx
// models/index.js
db.User.hasMany(db.Comment, { foreignKey: 'author', sourceKey: 'id'});
db.Comment.belongsTo(db.User, { foreignKey: 'author', targetKey: 'id'});
```

사용자의 id를 'author'라는 이름으로 댓글 모델의 외래키로 설정한다.

모델의 정의와 모델간 관계 설정이 끝났으면 sequelize를 동기화한다.

```jsx
// app.js
var { sequelize } = require('./models');

var app = express();
sequelize.sync();
```

sequelize.sync()가 실행되면 index.js 내에서 작성된 Sequelize 작업이 실제 DB와 동기화되면서 실행된다. 정의한 모델이 테이블에 없으면 새로 테이블을 생성한다.

### 라우터 & 시퀄라이즈 쿼리

express에서 클라이언트의 요청별로 응답을 할 수 있도록 라우터를 만들어준다.

```jsx
// routes/users.js
const express = require('express');
const router = express.Router();
const { User } = require('../models')

/* GET users listing. */
router.get('/', (req, res, next) {
});

router.post('/', (req, res, next) => {
  
});

module.exports = router;
```

```jsx
// routes/comments.js
const express = require('express');
const router = express.Router();
const { User, Comment } = require('../models');

router.get('/:id', (req, res, next) => {
    
});

router.post('/', (req, res, next) => {
    
});

router.patch('/:id', (req, res, next) => {
    
});

router.delete('/:id', (req, res, next) => {

});

module.exports = router;
```

- url 주소에서 변하는 부분은 `: 파라미터`(와일드카드)를 사용해 **req.parmas.파라미터**로 가져올 수 있다.
- 시작부분이 같은 두 라우터가 있다면 와일드카드 라우터가 더 뒤에 위치해야 한다.

    ```jsx
    router.get('/comments/7', () =>{});
    router.get('/comments/:id', () =>{});
    ```

- 라우팅 메소드 내에 sequelize를 이용한 DB 처리 로직을 작성한다.

```jsx
// routes/users.js
router.get('/', (req, res, next) {
  // User 테이블에서 데이터 가져와 json 형태로 응답
	User.findAll()
    .then((users) => {
      res.json(users);
    })
    .catch((err) => {
      console.error(err);
      next(err);
    })
});

router.post('/', (req, res, next) => {
  // User 테이블에 데이터 생성
	User.create({
    name: req.body.name,
    age: req.body.age,
    married: req.body.married,
  })
    .then((result) => {
      console.log(result);
      res.status(201).json(result);
    })
    .catch((err) => {
      console.error(err);
      next(err);
    })
});
```

```jsx
// routes/comments.js
router.get('/:id', (req, res, next) => {
    Comment.findAll({
        // INNER JOIN
        include: {
            model: User,
            where: {id: req.params.id}
        }
    })
    .then((comments) => {
        console.log(comments);
      res.json(comments);
    })
    .catch((err) => {
      console.error(err);
      next(err);
    })
});

router.post('/', (req, res, next) => {
    Comment.create({
        author: req.body.id,
        comment: req.body.comment
    })
    .then((result) => {
        console.log(result);
        res.status(201).json(result);
    })
    .catch((err) => {
        console.error(err);
        next(err);
    })
});

router.patch('/:id', (req, res, next) => {
    Comment.update({
        comment: req.body.comment,
    }, {
        where: {id: req.params.id}
    })
    .then((result) => {
        console.log(result);
        res.json(result);
      })
      .catch((err) => {
        console.error(err);
        next(err);
      })
});

router.delete('/:id', (req, res, next) => {
    Comment.destroy({
        where: {id: req.params.id}
    })
    .then((result) => {
        console.log(result);
        res.json(result);
      })
      .catch((err) => {
        console.error(err);
        next(err);
      })
});

module.exports = router;
```

sequelize에서 JOIN은 **include** 옵션을 사용한다.

- include
    - required : false면 LEFT OUTER JOIN, true면 INNER JOIN
    - attribute : SQL의 select와 같다. 디폴트는 *로 모든 컬럼을 필터링한다.
- where : SQL의 where과 동일. 특정 상태를 만족하는 기록만 추출한다. include 내에서 외래키 이외의 JOIN 조건을 설정할 수도 있다.

**CRUD**

- CREATE : Model.create({key: value})
- READ : Model.findAll({where: {key: value}}), Model.findOne()
- UPDATE : Model.update({key: value}, {where: {key: value}})
- DELETE : Model.destroy({where: {key: value}})