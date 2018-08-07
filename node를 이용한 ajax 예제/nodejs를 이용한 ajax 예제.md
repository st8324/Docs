## Node.js를 이용한 Ajax 예제

### 설치 항목

* 미리 설치 되어 있어야 하는 항목
  1. node.js  [다운로드 링크](https://nodejs.org/ko/ ) 
  2. MySQL [다운로드 링크](https://dev.mysql.com/downloads/file/?id=479861)
  3. express-generator
     * 실행 명령어 : npm install express-generator -g

### 과정

* 아래 과정을 순서대로 실행

* express를 이용한 프로젝트 생성

  * 실행 명령어 : express 프로젝트명
  * 예시  : express test

* 생성한 프로젝트로 이동

  * 실행 명령어 : cd 프로젝트명
  * 예시 cd test

* 기본 모듈 설치

  * 실행 명령어 : npm intall

* ejs 파일을 읽어오기 위한 ejs 모듈 설치

  * npm install ejs

* mysql 모듈 설치

  * npm install mysql

* 코드 수정

  1. app.js 수정

     ```js
     var createError = require('http-errors');
     var express = require('express');
     var path = require('path');
     var cookieParser = require('cookie-parser');
     var logger = require('morgan');
     
     var indexRouter = require('./routes/index');
     var signupRouter = require('./routes/signup');
     
     var app = express();
     
     // view engine setup
     app.set('views', path.join(__dirname, 'views'));
     app.set('view engine', 'ejs');
     
     app.use(logger('dev'));
     app.use(express.json());
     app.use(express.urlencoded({ extended: false }));
     app.use(cookieParser());
     app.use(express.static(path.join(__dirname, 'public')));
     
     app.use('/', indexRouter);
     app.use('/signup', signupRouter);
     
     // catch 404 and forward to error handler
     app.use(function(req, res, next) {
       next(createError(404));
     });
     
     // error handler
     app.use(function(err, req, res, next) {
       // set locals, only providing error in development
       res.locals.message = err.message;
       res.locals.error = req.app.get('env') === 'development' ? err : {};
     
       // render the error page
       res.status(err.status || 500);
     });
     
     module.exports = app;
     ```

  2. signup.js 생성

     * 프로젝트명/routes 폴더 밑에 생성

     ```js
     var express = require('express');
     var router = express.Router();
     var mysql = require('mysql');
     
     var pool = mysql.createPool({
       host : 'localhost',
       port : 3306,
       user : '아이디',
       password : '비밀번호',
       database : 'DB명',
       connectionLimit : 20,
       waitForConnections : false
     });
     
     /* GET users listing. */
     router.get('/', function(req, res, next) {
       res.render('signup',{});
     });
     
     router.post('/', function(req, res, next) {
     	var id = req.body.id;
     	var pass = req.body.pw;
     	
     	pool.getConnection(function(err, connection) {
     		if(err) {
     			throw err;
     		}
     
     		var sql = "INSERT INTO user (id, pw) VALUES "
     				+"('"+id+"','"+pass+"');";
     
     		console.log(sql);
     
     		connection.query(sql, function(err, rows) {
     			if(err) {
     				connection.release();
     				throw err;
     			}
     
     			connection.release();
     			res.redirect("/signup");
     		});	
     	});
     });
     
     router.post('/dup', function(req, res, next) {
     	var id=req.body.id;
     	pool.getConnection(function(err, connection) {
     		var sql = "SELECT count(id)"
     				+"FROM user where id ="+id +";"
     		connection.query(sql, (err,results, field)=>{
     			console.log(results);
     			res.send({cnt:results});
     		}
     		)
     	});
     });
     module.exports = router;
     ```

  3.  signup.ejs 생성

     * 프로젝트명/views 폴더 밑에 생성

     ```ejs
     <!DOCTYPE html>
     <html lang="en">
     <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0">
         <meta http-equiv="X-UA-Compatible" content="ie=edge">
         <title>Document</title>
     </head>
     <body>
         <div>
             <form method="POST">
                 <div><input type="text" id="id" name="id"></div>
                 <div><button type="button" id="dup">아이디 중복확인</button></div>
                 <div><input type="text" id="pw" name="pw"></div>
                 <div><input type="text" id="pwConfirm" name="pwConfirm"></div>
                 <div><button type="submit">제출</button></div>
             </form>
         </div>
         <script src="//code.jquery.com/jquery-3.3.1.js"></script>
         <script>
             var dup;
             $('#dup').click( function() {
                 var id = "";
                 id = $('#id').val();
                 $.ajax({
                     url: 'http://localhost:3000/signup/dup',
                     dataType: 'json',
                     type: 'POST',
                     data: {'id':id},
                     success: function(data) {
                         dup = data['cnt'][0]['count(id)'];
                         if(dup == 1){
                             alert("id 중복");
                         }
                         else{
                             alert("사용 가능 id");
                         }
                     }
                 });
             })
         </script>
     </body>
     </html>
     ```

* 서버 실행

  * 실행 명령문 : node bin/www

* 정리

  ```
  express test
  cd test
  npm install
  npm install ejs
  npm install mysql
  코드 수정 및 생성 작업
  node bin/www
  ```

  

