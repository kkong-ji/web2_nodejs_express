# web3_nodejs_express
생활코딩 강의를 들으며 학습한 nodejs의 framework express


## Nodejs, 그리고 Express의 등장
- 2009년 Ryan Dahl에 의해 자바스크립트 런타임 Nodejs가 개발됨
- 기존의 web browser를 구성할 수 있는 언어(Javascript)로 클라이언트와 서버를 쉽게 구현가능

<br>

> ❗그러나,

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

## Middleware (미들웨어)
> 쉽게 말해, 다른 사람이 만든 소프트웨어

<br>

### 미들 웨어의 종류
1. body-parser
  #### Installation
   `npm install body parser`

  #### API
  `var bodyParser = require('body-parser')`
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
  
  
