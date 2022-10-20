---
title: 'Node.js学习与MongoDB快速上手'
date: 2022-10-14 15:06:25
tags: [nodejs,笔记]
published: true
hideInList: false
feature: 
isTop: false
---
# Node.js简易入门
## 1、http库
```javascript
//导入http库
const http = require("http");
const url = require("url");
//创建服务
http.createServer(function(request,response){
    console.log(request.url);
    //设置响应头
    response.writeHead(200,{"Content-Type":"text/html"});
    //在页面上输出一句话，并结束响应
    response.write("<h1>hello world</h1>");
    //过滤url中的图标请求
    if(request.url != "/favicon.ico"){
        if(request.url == "get"){
            var quer = url.parse(request.url,true).query;
            var b = quer.a;
            console.log(typeof(b));
        }
    }
    if(request.url == "/test"){
        console.log("this is my test 111");
    }
    if(request.url == "/text"){
        response.write("ready to text");
        //console.log("ready to text");
    }
    response.end("<h1>hello world</h1>");
}).listen(8081);//监听8081端口

console.log("Server running at http://127.0.0.1:8081/");
```
## 2、async与await，Promise
node.js中许多操作都是异步操作，而异步操作中的数据不能直接进行retrurn，异步操作中的数据可封装成Promise再进行返回，同时结合async，await关键字操作。如下：
```javascript
const { rejects } = require("assert");
const fs = require("fs");
const { resolve } = require("path");

function a(filename){
    
    return new Promise((resolve,rejects)=>{	//resolve为正常情况的返回数据，rejects为函数出错情况下的返回数据
        //fs.readFile是异步操作，将其封装成Promise，这样子后a()也是一个异步函数
        fs.readFile("./static/"+filename,(err,data)=>{
            //如果读取文件出错,保存rejects
            if(err){
                console.log(err);
                rejects(err);
                return;
            }
            //无误保存resolve
            resolve(data);
        })
    });
}

async function getA(){
    // a()是异步函数，await的作用是等待a()内部的操作执行完再进行下一步操作，需要配合async关键字才能使用
    var data = await a("test.txt");
    console.log(data.toString());
}
getA();
```
## 3、粗糙的静态web服务器
使用**exports.模块名**进行导出，以便在其他文件下可以调用
**common.js文件**
```javascript
exports.getMine = function(filename){
    switch(filename){
        case ".html":
            return 'text/html';
        case ".css":
            return 'text/css';
        case ".js":
            return 'text/javascript';
        default:
            return 'text/html';
    }
}
```
**服务器文件**
```javascript
const http = require("http");
const fs = require("fs");
const common = require("./common.js");
const path = require("path");
//创建服务
http.createServer(function(req,resp){
    //获取url中访问的路径
    var filename = req.url;
    if(filename == "/"){
        filename = "/index.html";
    }

    var extname = path.extname(filename);

    if(filename != "/favicon.ico"){
        console.log(filename)
        fs.readFile("static"+filename,function(err,data){
            //如果文件读取失败，返回一下文字
            if(err){
                resp.writeHead(404,{"Content-Type":"text/plain;charset=utf-8"});
                resp.end("您访问的页面不存在");
            }
            //如果文件读取成功，返回文件内容以及对应的响应格式
            let mine = common.getMine(extname);
            resp.writeHead(200,{"Content-Type":mine+";charset=utf-8"});
            resp.end(data);
        })
    }


}).listen(8081);
```
## 4、post表单数据获取
在node中获取post数据使用到以下中间件
```javascript
const express = require("express")	

var app = new express()
app.use(express.json())			//设置接收axios applicate/json类型	实测没用，最好还是确保类型是application/x-www-form-urlencoded

app.use(express.urlencoded({extended:false}))		//自动将post参数转换为对象


/***
***	示例如下
***
***/
app.post("/updata",(req,resp)=>{
    let msg = req.body["msg"]
    resp.send("您提交的信息是:"+msg)
})
```
html中的代码
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <form action="http://127.0.0.1:8001/updata" method="post">
        <input type="text" name="msg">
        <input type="submit" value="提交">
    </form>
</body>
</html>
```
## 5、cookie的获取
cookie的获取也需要导入**cookie-parser**的模块
```javascript
const cookieParser = require("cookie-parser")

var app = express()
app.use(cookieParser())

/**
	其中，resp.cookie中{}包含以下参数
	maxAge：cookie有效时间，单位毫秒
	
**/

app.get('/chick',function(req,resp){
	resp.cookie("username","张三",{maxAge:1000*60*60})	//第三位参数是cookie的有效时间，单位毫秒(ms)
	resp.send("cookie已经添加")
})


app.get("/getcookie",function(req,resp){
    //console.log(req.cookies["username"])
    console.log(req.cookies)
    resp.send("你好")
    //resp.send("您的cookie是:"+req.cookies.username)
})
```
**加密cookie**，作用：用于传递一些中文cookie，先加密，需要的时候解密
```javascript
const cookieParser = require("cookie-parser")
//配置cookieParser中间件
app.use(cookieParser("abcdef"))		//传入加密密钥

//加密设置
app.get("/setcookie",(req,res)=>{
    res.cookie("username","muouren7",{maxAge:1000*60*60,signed:true})
})

//获取加密后的cookie
app.get("getcookie",(req,res)=>{
    console.log(req.signedCookies["username"])
})
```
## 6、跨域
```javascript
//设置允许跨域访问该服务
app.all('*',function(req,res,next){
    res.header("Access-Control-Allow-Origin","http://localhost:8081");		//放行的域
    res.header("Access-Control-Allow-Methods","PUT,GET,POST,DELETE,OPTIONS");	//放行的方法
    res.header("Access-Control-Allow-Headers","X-Requestd-With");	
    res.header("Access-Control-Allow-Headers","Content-Type");	
    res.header("Access-Control-Allow-Credentials",true);			//是否可以传递cookie，如果为true，那么第一项里必须为具体的域名，不能为*
    next()
});
```
## 7、session的使用
首先需要安装**express-session**的包
```shell
npm install express-session --save
```
引入包
```javascript
const session = require("express-session")
```
设置官方文档提供的中间件
```javascript
app.use(session({
    secret:'keyboard cat',		//服务端生成session的签名，里面的字符串随便写
    name:"session_id",			//修改cookie中对应session-key的名称
    resave:true,				//强制保存session
    saveUninitialized:true,		//强制将未初始化的session存储
    cookie:{
        maxAge:1000*60*60,	
        secure:false		//true表示只有https连接才能访问session		
    },
    rolling:true		//每次登录重新把cookie的过期时间设置为maxAge
}))
```
session的使用
```javascript
// 设置session
app.get("/setsession",(req,res)=>{
    req.session.username = "muouren7";
    res.send("session已经存储")
})
//获取session
app.get("/getsession",(req,res)=>{
    let name = req.session.username;
    res.send("您的session是---"+name)
})

//销毁session
app.get("/destroySession",(req,res)=>{
    //方法1 将保存session的cookie时间设置为0，销毁全部session
    req.session.cookie.maxAge = 0

    //方法2 销毁指定session
    req.session.username = ""

    //方法3 destroy
    req.session.destroy()

})
```
## 8、将session保存在mongoDB
导入**connect-mongo**的包
```javascript
const session = require("express-session")
const MongoStore = require("connect-mongo")(session)

app.user(session({
    store: MongoStore.create({
        url:"你的mongoDb数据库地址",
        touchAfter:24*60*60				//不管发出多少请求，24小时内只更新一次session
    })
}))
```
## 9、路由模块化
将单个js文件内的路由拆分成多个js文件，可以理解为模块化
主文件**app.js**
```javascipt
const express = require("express")
var app = express()

//引入模块admin.js，这里admin.js与app.js位于同一级目录下
var admin = require("admin")
//配置中间件
app.use("/admin",admin)

app.listen(8080,()=>{
    console.log("已经在端口8080开启服务");
})
```
模块文件**admin.js**
```javascript
const express = require("express")

var router = express.Router()

//url:http:127.0.0.1:8080/admin
router.get("/",(req,res)=>{
    res.send("您已来到管理员模块")
})

//url:http:127.0.0.1:8080/admin/manage
router.get("/manage",(req,res)=>{
    res.send('您正在管理admin模块')
})

//
module.exports = router
```
## 10、结合multer上传图片
安装**multer**
```shell
npm install --save multer
```
html代码如下，form表单中格式必须为**multipart/form-data**
```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <form action="http://127.0.0.1:8080/profile" method="post" enctype="multipart/form-data">
        <input type="file" name="pic" >
        <input type="submit" value="提交">
    </form>
</body>
</html>
```
**app.js**中代码如下
```javascript
const multer = require("multer")
const path = require("path")

const storage = multer.diskStorage({
    //配置文件上传位置
    destination: function (req, file, cb) {
        cb(null, '/static/files')
    },
    //修改上传文件的后缀名
    filename: function (req, file, cb) {
        //获取后缀名
        let extname = path.extname(file["originalname"])
        //设置后缀名
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9)
        cb(null, file.fieldname + '-' + uniqueSuffix + extname)
    }
})
const upload = multer({ storage: storage })

app.post("/profile",upload.single('pic'),(req,res,nex)=>{
    console.log(req.file)
    res.send(req.file)
})
```
## 11、node.js连接mongoDB
如下
```javascript
const MongoClinet = require("mongodb").MongoClient
const DataBaseUrl = "mongodb://127.0.0.1:27017/"		//mongoDB数据库地址
var app = express()

app.get('/chick',function(req,resp){
    let parse = url.parse(req.url)
    let name = parse.query.split("=")[1]
    MongoClinet.connect(DataBaseUrl,function(err,db){
        if(err){
            console.log("数据库连接错误");
        }
        let dbo = db.db("itying")
        let data = {"name":unescape(name)}
        let msg = dbo.collection("user").find(data).toArray((err,result)=>{
            if(err){
                console.log("查询出错")
            }
            resp.cookie("username",result[0]["name"],{maxAge:1000*60*60})
            resp.send(result)
            db.close()
        })
    })
})
```
# Mongoose使用
## 1、Demo
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://127.0.0.1:27017/itying")  //把数据库名字也加上
//操作users表 定义一个Schema
var UserSchema = mongoose.Schema({
    name:String,
    age:Number,
    status,Number
})
//定义model，第一个参数（表名必须为第一个参数的复数形式，且参数首字母必须大写），第二参数为定义的Schema，第三个参数（如果有，则对应此参数对应的集合，没有则是以第一个参数为准）
var User = mongoose.model('User',UserSchema,"users")
//查询users集合的数据，第一个参数为过滤条件
User.find({},(err,doc)=>{
    if(err){
        console.log(err)
    	return;
    }
    console.log	(doc)
})
```
## 2、增加一条数据
```javascript
var u = new User({
    name:"muouren7",
    age:22
})
u.save((err,msg)=>{
    if(err){
        console.log("err:"+err)
        return
    }
    if(msg){
        console.log("msg:"+msg)
    }
})    //执行增加操作，回调函数可加可不加，msg为user的json数据
```
## 3、查询数量
```javascript
//2、查询users表数量，第一个{}中为过滤条件
User.countDocuments({},(err,num)=>{
    if(err){
        console.log(err)
        return
    }
    console.log("返回值是:"+num)
})
```
## 4、修改数据记录
```javascript
//4、修改数据记录，参数1表示修改的条件，参数2表示修改的内容
//例：将名字为张三的改为张三1234
User.updateOne({name:"张三"},{name:"张三1234"},(err,res)=>{
    if(err){
        console.log(err)
        return
    }
    console.log(res)
})
```
## 5、删除数据
```javascript
// 5、删除记录
User.deleteOne({name:"zhangsan"},(err,res)=>{
    if(err){
        console.log(err)
    }
    console.log(res)
    console.log("删除成功")
})
```
# MongoDB
## 1、数据库的创建、使用
在cmd下运行，直接切换到数据库不能创建成功，必须插入一条数据
```shell
use itying 		###创建名为itying的数据库
```
往itying数据库的user集合里插入一条数据（集合不用创建，直接操作）
```shell
use itying		###切换到itying数据库下
db.user.insert({"name":"xiaoming","age":20});

show collections	###展示当前数据库里所有的集合
db.user.find()		###展示user集合下的数据

show dbs	### 展示当前所有的数据库
```
## 2、删除集合
```shell
### 切换到itying数据库下
use itying
###删除user集合
db.user.drop()
```
## 3、删除数据库
```shell
### 切换到itying数据库下
use itying
### 删除当前数据库
db.dropDatabase()
```
## 4、插入数据
```shell
db.表名.insert({"name":"zhangsan","age":20});
```
## 5、查询
```shell
###	1、查询所有记录
db.user.find()

###	2、查找age=22的数据
db.user.find({"age":22})

###	3、查询age>22的记录
db.user.find({age:{$gt:22}})

###	4、查询age<22的记录
db.user.find({age:{$lt:22}})

###	5、查询age>=25的记录
db.user.find({age:{$gte:25}})

###	6、查询age<=25的记录
db.user.find({age:{$lte:25}})

###	7、查询age>=23 并且 age<=26
db.user.find({age:{$gte:23,$lte:26}})

###	8、查询name中包含mongo的数据	模糊查询
db.user.find({name:/mongo/})

###	9、查询name中以mongo开头的数据
db.user.find({name:/^mongo/})

###	10、查询指定列,只显示name，不显示age，第一个{}为条件参数
db.user.find({age:{$lte:25}},{name:1})

### 11、按照年龄排序，1：升序	2：降序
db.user.find().sort({age:1})

### 12、查询前5条数据
db.user.find().limit(5)

###	13、查询10条以后的数据
db.user.find().skip(10)

### 14、查询5-10之间的数据
db.user.find().limit(10).skip(5)

### 15、or查询,查22或者25
db.user.find({$or:[{age:22},{age:25}]})

### 16、统计age>25的个数
db.user.find({age:{$gt:25}}).count()
```
## 6、修改
```shell
###	1、把名字叫做小明的年龄改为16
db.user.update({"name":"小明"},{$set:{"age":16}})
```
## 7、删除
```shell
### 1、删除age为26的
db.user.remove({age:26})
```
## 8、设置索引与复合索引（面临海量数据时提升查询速度）
```shell
###	对username字段设置索引，1：升序		2：降序
db.user.ensureIndex({"username":1})
db.user.createIndex({"username":1})		##新版mongo命令

###	获取当前已经设置的索引
db.user.getIndexes()

###	删除索引
db.user.dropIndex({"username":1})

### 复合索引
db.user.ensureIndex({"usernam":1,"age":-1})

###	设置唯一索引（必须唯一）
db.user.exsureIndex({"userid":1},{"unique":true})		###	设置userid为唯一索引，数据库中不能存在相同的userid
```
## 9、mongo权限配置
1、创建超级管理员用户
```shell
use admin
db.createUser({
	user:'admin',
	pwd:'123456',
	roles:[{role:'root',db:'admin'}]
})
```
2、修改mongodb安装目录下的配置文件mongod.cfg文件，添加以下配置，开启连接验证，然后重启mongodb服务
```shell
security:
	authorization: enabled;
```
3、用超级管理员用户密码连接数据库
```shell
mongo admin -u 用户名 -p 密码
mongo 127.0.0.1:27017/test -u user -p pwd
```
4、给eggcms数据库创建一个用户，只能访问eggcms不能访问其他数据库
```shell
user eggcms
db.createUser({
	user:'eggadmin',
	pwd:'123456',
	roles:[{role:'dbOwner',db:'eggcms'}]
})
```
## MongoDb数据库角色
1、数据库用户角色：read、readWrite
2、数据库管理角色：dbAdmin、dbOwner、userAdmin

3、集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager

4、备份恢复角色：backup、restore

5、所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase

6、超级用户：root
```shell
### 查看当前库下的管理员
show user

###	删除用户
db.dropUser("eggadmin")

###	修改用户密码
db.updateUser("admin",{pwd:'password'})

### 密码认证
db.auth("admin","password")

```













