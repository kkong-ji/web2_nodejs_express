# web3_nodejs_express
생활코딩 강의를 들으며 학습한 nodejs의 framework express


<br>
<br>


## Nodejs, 그리고 Express의 등장
- 2009년 Ryan Dahl에 의해 자바스크립트 런타임 Nodejs가 개발됨
- 기존의 web browser를 구성할 수 있는 언어(Javascript)로 클라이언트와 서버를 쉽게 구현가능

<br>

> but❗

- nodejs는 서버를 구현함에 있어서 고려해야할 사항이 너무 많음을 인지하게됨.
  <br> (HTTP request, 쿠키 파싱, 세션관리, 라우팅 구성 등)
- 방대한 양의 코드와 복잡한 작동원리 개선을 위해 "Express" 프레임워크가 탄생.

<br>

## Express의 특징
- Express는 nodejs에서 보편적으로 사용되는 프레임워크
- 다음과 같은 매커니즘 제공
  - HTTP 통신 요청(Request; GET, POST, DELETE 등)에 대한 핸들러를 만든다.
  - 템플릿에 데이터를 넣어 응답(response)을 만들기 위해 view의 렌더링 엔진과 결합(integrate)한다.
  - 접속을 위한 포트나 응답 렌더링을 위한 템플릿 위치같은 공통 웹 어플리케이션 세팅을 한다.
  - 핸들링 파이프라인(reqest handling pipeline) 중 필요한 곳에 추가적인 미들웨어 처리 요청을 추가한다.
 
> 라우터(router) 기능을 통해 서버와 통신

<br>

`router`

```
app.get('/', function (req, res) {
  res.send('Hello World!');
});
```

<br>

## Routing (라우팅)
> URL 또는 특정한 HTTP 요청 메소드 (GET, POST) 에 대한 클라이언트 요청에 응답하는 방법을 선택하는 것.
### 라우팅의 예시

<br>

① 홈 페이지(루트)에서 `Hello World!` 로 응답 (Get 방식)

```
app.get('/', function (req, res) {
  res.send('Hello World!');
});
```

② 홈 페이지(/)에서 POST 요청에 응답
```
app.post('/', function (req, res) {
  res.send('Got a POST request');
});
```

③ `/user` 라우트에 대한 PUT 요청에 응답
```
app.put('/user', function (req, res) {
  res.send('Got a PUT request at /user');
});
```

④ `/user` 라우트에 대한 DELETE 요청에 응답
```
app.delete('/user', function (req, res) {
  res.send('Got a DELETE request at /user');
});
```

## Middleware (미들웨어)
### 미들 웨어의 예시

<br>

### 1. Third-party Middleware
> Express가 제공하는 것이 아닌 제3자. 즉, 타인이 만든 소프트웨어

<br>

### ① body-parser
> HTTP API 통신 (post, put) 시 request body에 들어오는 데이터 값을 읽을 수 있는 구문으로 파싱해줌과
> <br>
> 동시에 `req.body` 로 입력해주어 `body` 인자를 쓸 수 있게 해주는 미들웨어

<br>

#### ⚙️ Installation
`npm install body parser`

#### API
`var bodyParser = require('body-parser')`
<br>
`app.use(bodyParser.urlencoded({ extended: false}))`

#### 사용 예시
`기존코드`
  ```
    app.post('/create_process', function(request, response) {
      var body = '';
      request.on('data', function(data) {
          body = body + data;
      });
      request.on('end', function() {
          var post = qs.parse(body);
          var title = post.title;
          var description = post.description;
          fs.writeFile(`data/${title}`, description, 'utf8', function(err) {
            response.writeHead(302, {Location: `/?id=${title}`});
            response.end();
          })
      });
  ```
  
  `적용코드`
  ```
      app.post('/create_process', function(request, response) {
          var post = request.body;
          var title = post.title;
          var description = post.description;
          fs.writeFile(`data/${title}`, description, 'utf8', function(err) {
            response.writeHead(302, {Location: `/?id=${title}`});
            response.end();
          })
      });
  ```


### ② compression 

> 웹 페이지의 데이터 크기가 클 경우, 페이지를 압축하기 위한 미들웨어
> <br>
> 데이터를 전송할 때 압축된 데이터가 전송되기 때문에 데이터의 양을 크게 줄일 수 있음.
<br>

#### ⚙️ Installation
`npm install compression`

#### API
`var compression = require('compression')`
<br>
`app.use(compression())`


<br>

### 2. Application-level Middleware
> `Get`, `Put`, `Post` 와 같은 요청 메소드를 처리할 수 있는 미들웨어
  
#### 사용 예시

#### ① 경로가 없는 미들웨어
```
var app = express();

app.use(function (req, res, next) {
  console.log('Time:', Date.now());
  next();
});
```

#### ② 경로가 있는 미들웨어
```
app.use('/user/:id', function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});
```

#### ③ 경로에 대한 GET 요청 처리 미들웨어
```
app.get('/user/:id', function (req, res, next) {
  res.send('USER');
});
```

#### ③ 경로에 대한 연속적인 처리 미들웨어
```
app.use('/user/:id', function(req, res, next) {
  console.log('Request URL:', req.originalUrl);
  next();
}, function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});
```

<br>

### 3. Built-in Middleware 
> Express에서 기본으로 제공해주는 미들웨어
> <br>
> 정적인 파일을 서비스할 때 주로 사용하는 `static` 함수가 대표적

#### API
```
app.use(express.static('public'));
```

### 4. 오류 처리 미들웨어
> 오류 발생시 이를 핸들링할 수 있게 해주는 미들웨어

<br>

```
app.use(function(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

<br>

> ❗ 오류 처리 미들웨어는 다음과  다른 `app.use()` 및 라우트 호출을 먼저하고 마지막으로 사용해야함.

```
var bodyParser = require('body-parser');
var methodOverride = require('method-override');

app.use(bodyParser());
app.use(methodOverride());
app.use(function(err, req, res, next) {
  // logic
});
```

<br>

> 오류를 다음 항목으로 전달하고자 할 때
```
function clientErrorHandler(err, req, res, next) {
  if (req.xhr) {
    res.status(500).send({ error: 'Something failed!' });
  } else {
    next(err);
  }
}
```

<br>

## 보안
> 여러가지 공격으로부터 Express 애플리케이션을 지키기 위한 보안 항목

- 더 이상 사용되지 않거나 취약성이 있는 버전의 Express 사용 중지 -> Express를 자주 업데이트
- TLS 사용 -> 인터넷 HTTPS, Nginx 와 같은 보안이 적용된 전송 프로토콜을 사용
- `Helmet` 라이브러리 사용 -> 보안이슈들을 자동으로 해결해주는 미들웨어.
<br> `npm install --save helmet` 으로 설치.
- 쿠키를 안전하게 사용
- dependency(종속항목)이 안전한지 확인
<br> `npm i nsp -g` -> nsp 모듈을 통해 종속성 보안을 체크하는 것도 좋음
