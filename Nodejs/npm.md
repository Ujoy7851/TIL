## npm (node package manager)

자바스크립트 패키지 매니저로 Node.js에서 사용할 수 있는 모듈들을 패키지화해서 모아둔 저장소 역할과 패키지 설치 및 관리를 위한 CLI를 제공한다. 자신의 패키지를 공개할 수도 있고 검색해서 사용할 수도 있다.

## package.json

```bash
$ npm init
$ npm init -y
```

프로젝트 루트에서  `npm init` 명령어를 실행하고 프로젝트에 대한 정보를 입력하면 해당 정보를 바탕으로 **package.json** 파일을 생성한다. **package.json**은 프로젝트 정보와 패키지의 의존성을 관리한다. **package.json**을 이용하면 동일한 개발 환경을 빠르게 구축할 수 있다.

**package.json**
```json
{
  "name": "npmtest",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

- **name, version** : 패키지의 고유성을 판단하는 항목으로 생략할 수 없다.
- **dependencies** : 해당 프로젝트가 의존하는 패키지(참조하는 모듈)를 명시. `npm install` 명령을 사용해 패키지를 설치하면 **dependencies**에 이름과 버전이 기록된다.
- **devDependencies** : 트랜스파일러와 같이 개발 시에만 사용하는 개발용 의존 패키지를 명시한다. `npm install` 명령에 `--save-dev` (또는 `-D`)옵션을 사용하면 패키지 설치와 함께 **devDependencies**에 기록된다.

## 패키지 설치

```bash
$ npm install
$ npm install <package>
$ npm install <package>@version
$ npm install -g <package>
$ npm i <package>
```

- `npm install` 명령어를 사용해 package.json에 명시된 모든 의존 패키지를 한 번에 설치할 수 있다.
- 패키지명 뒤에 @버전을 추가해 패키지 버전을 지정하여 설치할 수 있다.
- 패키지는 프로젝트 루트 디렉토리에 **node_modules** 디렉토리가 생성되고 그 안에 설치된다.
- `--global` (또는 `-g`)옵션을 지정하면 패키지를 전역으로 설치한다.

## Semantic versioning

package.json의 dependencies에 있는 의존 패키지들은 sematic versioning을 통해 업데이트 범위를 지정할 수 있다.

![https://media.vlpt.us/images/iamjoo/post/e93c655b-4b5b-4423-8462-867c0177bb1b/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202020-06-10%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.03.08.png](https://media.vlpt.us/images/iamjoo/post/e93c655b-4b5b-4423-8462-867c0177bb1b/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202020-06-10%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.03.08.png)

- 간단히 major는 대규모 변화, minor는 신기능 추가, patch는 버그 수정을 의미한다고 이해하면 된다. major 버전의 업데이트는 신중히 하는 것이 좋다.
- `~` 틸트 : 패치 버전 범위 내에서 업데이트
    - ~0.1.1 : 0.1.1 <= version < 0.2.0
- `^` 캐럿 : 마이너 버전 범위 내에서 업데이트
    - ^1.0.2 : 1.0.2 <= version < 2.0

## npm 명령어

**패키지 업데이트**

```bash
$ npm update <package>
$ npm update
```

pakage.json에 명시한 버전으로 한꺼번에 업데이트할 수 있다.

**패키지 제거**

```bash
$ npm uninstall <package>
$ npm uninstall -g <package>
```

`npm remove`, `npm rm`, `npm r`, `npm un`도 가능

**업데이트 가능 패키지 확인**

```bash
$ npm outdated
```

**패키지 검색**

```bash
# npm에서 패키지명이 포함된 패키지 검색
$ npm search <package>
# 패키지 정보 (package.json) 검색
$ npm info <package>
# 프로젝트 내에서 패키지의 의존성 확인
$ npm ls <package>
```

## 패키지 배포

[www.npmjs.com](http://www.npmjs.com) 가입 후

```bash
# login
$ npm adduser

# 버전 업데이트
$ npm version patch
$ npm version minor
$ npm version major

# 패키지 배포
$ npm publish

# 패키지 삭제 (24이내에 가능)
$ npm unpublish <package> --force
```

### Reference

[https://poiemaweb.com/nodejs-npm](https://poiemaweb.com/nodejs-npm)