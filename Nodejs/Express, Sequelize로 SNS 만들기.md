### 프로젝트 구조 세팅

**node 개발환경 구성**

```bash
npm init
```

package.json에서 scripts에 start 명령어 추가

`"start": "nodemon app"`

**nodemon**은 서버에 변경사항이 있을 때 자동으로 재시작해주는 모듈이다.

```bash
npm i -D nodemon
npm i -g nodemon
```

**sequelize 개발 환경 구성**

```bash
npm init
npm i sequelize mysql2
npm i -g sequelize
sequelize init
```

**express 개발 환경 구성**

```bash
npm i express cookie-parser express-session morgan connect-flash pug
```

```jsx
// app.js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const flash = require('connect-flash');

const app = express();

app.set('view engine', 'pug');
app.set('view', path.join(__dirname, 'view'));
app.set('port', process.env.PORT || 8001);

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extended: false}));
app.use(cookieParser('nodebirdsecret'));
app.use(session({
    resave: false,
    saveUninitialized: false,
    secret: 'nodebirdsecret',
    cookie: {
        httpOnly: true,
        secure: false
    }
}));
app.use(flash());

app.listen(app.get('port'), () => {
    console.log(`${app.get('port')}번 포트에서 서버 실행중...`);
})
```

루트 디렉토리에 **public**, **view** 폴더 생성

위 코드의 경우 cookie와 session의 비밀키 값이 코드 상에 노출되어 있어서 보안에 취약하다. **dotenv** 패키지를 이용하면 노출하고 싶지 않은 값들을 환경변수로 설정해서 사용할 수 있다.

```bash
npm i dotenv
```

루트 디렉토리에 **.env** 파일을 생성하고 관리할 값들을 작성한다. 후에 오픈소스로 프로젝트를 공개할 때 .gitignore를 통해 .env 파일을 제외하면 민감한 정보들을 안전하게 지킬 수 있다.

```
// .env
COOKIE_SECRET= nodebirdsecret
```

.env에 작성한 값이 **process.env** 환경변수에 설정된다.

```jsx
// app.js
require('dotenv').config();

app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
    resave: false,
    saveUninitialized: false,
    secret: process.env.COOKIE_SECRET,
    cookie: {
        httpOnly: true,
        secure: false
    }
}));
```

### 라우터 생성

```jsx
// app.js
const indexRouter = require('./routes');
app.use('/', indexRouter);
```

### DB 테이블 관계 설정

User와 Post는 일대다 관계

Post와 Hashtag는 다대다 관계

User끼리의 팔로우도 다대다 관계

User와 Post의 좋아요는 다대다 관계

```jsx
db.User = require('./user')(sequelize, Sequelize);
db.Post = require('./post')(sequelize, Sequelize);
db.Hashtag = require('./hashtag')(sequelize, Sequelize);

db.User.hasMany(db.Post);
db.Post.belongsTo(db.User);

// PostHashtag란 이름의 새로운 관계 모델 생성
db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag'});
db.Hashtag.belongsToMany(db.Post, { through: 'PostHashtag'});

db.User.belongsToMany(db.User, { through: 'Follow, as: "Follwers', foriegnKey: 'followingId' });
db.User.belongsToMany(db.User, { through: 'Follow', as: 'Following', foriegnKey: 'followerId' });

db.User.belongsToMany(db.Post, { through: 'Like' });
db.Post.belongsToMany(db.User, { through: 'Like' });
```

### 로그인, 회원가입 기능 구현

```bash
npm i passport passport-local passport-kakao bcrypt
```

- passport : 인증 미들웨어
- passport-local : 이메일 계정으로 로그인
- passport-kakao : 카카오 계정으로 로그인
- bcrypt : 암호화

루트 디렉토리 아래에 passport 폴더 생성 후, index.js, localStrategy.js, kakaoStrategy.js 파일 생성해서 로그인 관련 strategy를 세팅해준다.

```jsx
// /passport/localStrategy.js
const { User } = require('../models');

const LocalStrategy = require('passport-local').Strategy;
const bcrypt = require('bcrypt');

module.exports = (passport) => {
    passport.use(new LocalStrategy({
        usernameField: 'email',
        passwordField: 'password'
    }, async (email, password, done) => {    // done(에러, 성공, 실패)
        try {
            const exUser = await User.findOne({ where: { email }});
            if(exUser) {
                const result = await bcrypt.compare(password, exUser.password);
                if(result) {
                    done(null, exUser);
                } else {
                    done(null, false, { message: '비밀번호가 일치하지 않습니다.' });
                }
            } else {
                done(null, false, { message: '가입되지 않은 회원입니다.' });
            }
        } catch(error) {
            console.error(error);
            done(error);
        }
    }));
}
```

localStrategy는 이메일 계정으로 로그인하는 기능을 담당한다. **passport-local** 모듈을 사용하며 **LocalStrategy**는 urlencoded 미들웨어가 해석한 req.body.email, req.body.password 값들을 usernameField, passwordFile에 연결해준다.

```jsx
// /passport/index.js
const local = require('./localStrategy');
const kakao = require('./kakaoStrategy');

module.exports = (passport) => {
    local(passport);
    kakao(passport);
}
```

```jsx
// app.js
const passport = require('passport');

const passportConfig = require('./passport');

passportConfig(passport);

app.use(passport.initialize());
app.use(passport.session());
```

보통 웹 응용 프로그램에서 사용자를 인증하기 위한 사용자 정보는 로그인 요청 시에만 전송된다. 한 번 인증에 성공하면 브라우저에 설정된 쿠키를 통해 세션이 설정되고 유지된다. 이후 요청에서는 세션을 식별하는 쿠키를 통해 로그인 상태를 유지한다. passport에서는 로그인 세션을 관리하기 위해 passport.session 미들웨어를 사용한다. (참고로, app.use(session())보다 아래에 작성해줘야 한다.)

```jsx
// /routes/auth.js
const express = require('express');
const router = express.Router();
const bcrypt = require('bcrypt');
const passport = require('passport');
const { User } = require('../models');

router.post('/join', async (req, res, next) => {
    const { email, nick, password } = req.body;
    try {
        const exUser = await User.findOne({ where: { email }});
        if(exUser) {
            req.flash('joinError', '이미 가입된 이메일입니다.');
            return res.redirect('/join');
        } else {
            const hash = await bcrypt.hash(password, 12);
            await User.create({
                email,
                nick,
                password: hash
            });
            return res.redirect('/');
        }
    } catch(error) {
        console.error(error);
        next(error);
    }
});

router.post('/login', (req, res, next) => {
    passport.authenticate('local', (authError, user, info) => {
        // done(에러, 성공, 실패)가 authError, user, info에 전달됨
        if(authError) {
            console.error(authError);
            return next(authError);
        }
        if(!user) {
            req.flash('loginError', info.message);
            return res.redirect('/');
        }
        return req.login(user, (loginError) => {
            if(loginError) {
                console.error(loginError);
                return next(loginError);
            }
            return res.redirect('/');
        })
    })(req, res, next);
});

router.get('/logout', (req, res, next) => {
    req.logout();
    req.session.destroy();
    res.redirect('/');
})

module.exports = router;
```

routes 폴더 아래 auth.js 파일을 만들어 /auth로 요청되는 회원가입, 로그인에 대한 라우터를 생성한다.

**/auth/join** endpoint로 POST 요청이 들어온 경우 회원가입을 진행한다. 비밀번호는 bcrypt 모듈을 이용해 해시 값으로 암호화 시킨다. bcrypt.hash()의 인자로 숫자 값을 받는데 이 값이 높을수록 암호화 강도가 높아진다. 암호화에 1초 정도 걸리도록 값을 조정하는 것이 적당하다.

**/auth/login**으로 요청이 들어오면 **passport.authentication()**를 이용해 로컬 로그인 인증을 진행한다. 두번째 인자로 받는 콜백함수의 매개변수는 LocalStrategy에서 done()의 인자로 넘긴 값을 전달받는다. 이 값을 기준으로 로그인 인증 성공 유무를 확인해 **req.login()**을 통해 로그인을 하고 메인페이지로 이동시키거나 에러메세지를 전달한다. req.login() 로그인 세션을 설정하는데 사용하는 함수이다.

**/auth/logout**으로 로그아웃 요청이 들어오면 req.logout()을 실행해 세션에 저장된 유저 정보를 삭제한다. 추가적으로 req.session.destroy()를 호출할 수도 있는데 다른 세션 정보까지 지워질 수 있다.

```jsx
// /routes/middlewares.js
const { regexp } = require("sequelize/types/lib/operators")

exports.isLoggedIn = (req, res, next) => {
    if(req.isAuthenticated()) {
        next();
    } else {
        res.status(403).send('로그인 필요');
    }
};

exports.isNotLoggedIn = (req, res, next) => {
    if(!req.isAuthenticated()) {
        next();
    } else {
        res.redirect('/');
    }
}

// /routes/auth.js
router.post('/join', isNotLoggedIn, async (req, res, next) => {
    
});

router.post('/login', isNotLoggedIn, (req, res, next) => {
    
});
```

passport에서 추가해주는 **req.isAuthenticated()**를 이용해 로그인 상태인지 아닌지를 판별해 추가적인 동작을 하는 미들웨어를 만들어 라우터에 사용할 수 있다.

```jsx
// /passport/index.js
const local = require('./localStrategy');
const kakao = require('./kakaoStrategy');
const { User } = require('../models');

module.exports = (passport) => {
    passport.serializeUser((user, done) => {
        done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
        User.findOne({ where: { id } })
            .then(user => done(null, user))
            .catch(err => done(err));
    });

    local(passport);
    kakao(passport);
}
```

req.login()이 호출되면 **serializeUser** 미들웨어가 실행된다. serializeUser는 req.login의 첫번째 인자로 전달한 **user** 객체를 전달받아 세션(**req.session.passport.user**)에 저장한다. 위 코드에서는 user 객체를 전달받아 사용자의 id만 세션에 저장한다. (객체 전체를 저장해도 되지만 용량이 크기 때문에 id만 저장함)

**deserializeUser**는 서버에서 요청이 올 때마다 serializeUser에서 세션에 저장한 값을 실제 DB 데이터와 비교한다. 해당하는 유저 정보가 있으면 done의 두번째 인자를 req.user에 저장하고, 요청을 처리할 때 유저의 정보를 req.user를 통해 넘긴다. 위 코드에서는 serializeUser에서 id만 넘겼는데 id만으로 req.user를 만들 수 없기 때문에 완전한 user 객체를 만들어 done을 해준다.

```jsx
// /passport/index.js
const users = {};

passport.deserializeUser((id, done) => {
        if(users[id]) {
            done(null, user[id]);
        } else {
            User.findOne({ where: { id } })
            .then(user => {
                users[id] = user;
                done(null, user);
            })
            .catch(err => done(err));
        }
    });
```

deserializeUser는 매 요청마다 실행되기 때문에 DB 조회를 캐싱해서 효율적으로 만들 수 있다.

### 게시글 및 파일(이미지) 업로드

express에서 제공하는 express.json, express.urlencode는 파일을 받아서 저장하는 기능을 제공하지 않기 때문에 따로 **multer**라는 모듈을 설치해 파일을 처리하는 작업을 구현한다.

```bash
npm i multer
```

우선 프론트엔드에서 이미지나 파일을 서버로 전송 하려면 **form** 태그의 타입을 `enctype="multipart/form-data`로 설정해야 한다.

```html
// /views/main.js
form(action='/post' method='post' enctype='multipart/form-data')
          div
            label(for='img') 사진 업로드
            input#img(type='file' accept='image/*')
```

form을 통해 입력된 데이터가 post 방식으로 전송되었을 때 이를 처리할 라우터를 생성한다.

```jsx
const express = require('express');
const multer = require('multer');
const path = require('path');
const { Post, Hashtag } = require('../models');
const router = express.Router();
const { isLoggedIn } = require('./middlewares');

const upload = multer({
    storage: multer.diskStorage({
        destination(req, file, cb) {
            cb(null, 'uploads/');
        },
        filename(req, file, cb) {
            const ext = path.extname(file.originalname);
            cb(null, path.basename(file.originalname, ext) + new Date().valueOf() + ext);
        }
    }),
    limit: { fileSize: 5 * 1024 * 1024},
});

router.post('/img', isLoggedIn, upload.single('img'), (req, res) => {
    console.log(req.file);
    res.json({ url: `/img/${req.file.filename}` });
});

const upload2 = multer();
router.post('/', upload2.none(), async (req, res, next) => {
    try {
        const post = await Post.create({
            content: req.body.content,
            img: req.body.url,
            UserId: req.user.id,
        });
        const hashtags = req.body.content.match(/#[^\s#]*/g);
        if(hashtags) {
            const result = await Promise.all(hashtags.map(tag => {
                return Hashtag.findOrCreate({
                    where: { title: tag.slice(1).toLowerCase() },
                })
            }));
            await post.addHashtags(result.map(r => r[0]));
        }
        res.redirect('/');
    } catch(error) {
        console.error(error);
        next(error);
    }
});

module.exports = router;
```

**/post/img**로 요청이 오면 multer 모듈을 통해 post 방식으로 전송된 파일을 처리한다.

- storage : 파일을 어디에 저장할지 (디스크, 클라우드 등)
- destination : 파일의 저장 경로
- filename : 파일이 저장될 이름. 위 코드에서는 파일명의 중복을 피하기 위해 현재 시간을 파일명에 포함시켰다.
- limit : 전송 용량 제한
- upload.single('img') : 이미지 하나를 받아오는 미들웨어로 매개변수가 form을 통해 전송되는 파일의 name 또는 id 속성과 일치해야 한다. **req.file**에 파일을 추가해준다.
- upload.array, upload.fields : 여러 개의 이미지를 받아올 수 있는 미들웨어
- upload.none : 아무것도 업로드하지 않는 경우

**/post**로 요청이 오면 Post 객체와 Hashtag 객체를 생성해 DB에 저장하는데 Post와 Hashtag가 **다대다** 관계를 가지기 때문에 `post.addHashtags` 함수를 호출해줘야 한다. add이외에도 [remove, set, get] + (as로 지정한 값 또는 모델명) 함수가 생긴다.