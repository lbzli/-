# Node.js 学习笔记
## 初识Node.js
### 什么是Node.js
- Node.js 是什么
  + JavaScript 运行时
  + 既不是语言，也不是框架，它是一个平台
- Node.js 中的 JavaScript
  + 没有 BOM、DOM
  + EcmaScript 基本的 JavaScript 语言部分
  + 在 Node 中为 JavaScript 提供了一些服务器级别的 API
    * 文件操作的能力
    * http 服务的能力
- Node 中的 JavaScript
  + EcmaScript
    * 变量
    * 方法
    * 数据类型
    * 内置对象
    * Array
    * Object
    * Date
    * Math
  + 模块系统
    * 在 Node 中没有全局作用域的概念
    * 在 Node 中，只能通过 require 方法来加载执行多个 JavaScript 脚本文件
    * require 加载只能是执行其中的代码，文件与文件之间由于是模块作用域，所以不会有污染的问题
      - 模块完全是封闭的
      - 外部无法访问内部
      - 内部也无法访问外部
    * 模块作用域固然带来了一些好处，可以加载执行多个文件，可以完全避免变量命名冲突污染的问题
    * 但是某些情况下，模块与模块是需要进行通信的
    * 在每个模块中，都提供了一个对象：`exports`
    * 该对象默认是一个空对象
    * 你要做的就是把需要被外部访问使用的成员手动的挂载到 `exports` 接口对象中
    * 然后谁来 `require` 这个模块，谁就可以得到模块内部的 `exports` 接口对象
    * 还有其它的一些规则，具体后面讲，以及如何在项目中去使用这种编程方式，会通过后面的案例来处理
  + 核心模块
    * 核心模块是由 Node 提供的一个个的具名的模块，它们都有自己特殊的名称标识，例如
      - fs 文件操作模块
      - http 网络服务构建模块
      - os 操作系统信息模块
      - path 路径处理模块
      - 。。。。
    * 所有核心模块在使用的时候都必须手动的先使用 `require` 方法来加载，然后才可以使用，例如：
      - `var fs = require('fs')`
- http
  + require
  + 端口号
    * ip 地址定位计算机
    * 端口号定位具体的应用程序
  + Content-Type
    * 服务器最好把每次响应的数据是什么内容类型都告诉客户端，而且要正确的告诉
    * 不同的资源对应的 Content-Type 是不一样，具体参照：http://tool.oschina.net/commons
    * 对于文本类型的数据，最好都加上编码，目的是为了防止中文解析乱码问题
  + 通过网络发送文件
    * 发送的并不是文件，本质上来讲发送是文件的内容
    * 当浏览器收到服务器响应内容之后，就会根据你的 Content-Type 进行对应的解析处理

### 代码风格

```javascript
var foo = 'bar'
var foo ='bar'
var foo= 'bar'
var foo = "bar"

if (true) {
  console.log('hello') 
}

if (true) {
    console.log('hello') 
}

if (true ){
      console.log('hello') 
}
```

为了约定大家的代码风格，所以在社区中诞生了一些比较规范的代码风格规范：dnsajkndkjsabnjkdnjksandjknsajkdnjkasnjkdnjksandjknsajkdnjksajkdnas

- [JavaScript Standard Style](https://standardjs.com/)
- Airbnb JavaScript Style
代码风格
- 无分号
  + `(`
  + `[`
  + `
  + 最好前面补分号，避免一些问题
  + 《编写可维护的 JavaScript》
  + 不仅是功能，还要写的漂亮
- 服务端渲染
  + 说白了就是在服务端使用模板引擎
  + 模板引擎最早诞生于服务端，后来才发展到了前端


#### Node 是一个比肩 Java、PHP 的一个平台
  + JavaScript 既能写前端也能写服务端

## Nodoe.js 的模块系统
### exports 和 module.exports 的区别
  + 每个模块中都有一个 module 对象
  + module 对象中有一个 exports 对象
  + 我们可以把需要导出的成员都挂载到 module.exports 接口对象中
  + 也就是：`moudle.exports.xxx = xxx` 的方式
  + 但是每次都 `moudle.exports.xxx = xxx` 很麻烦，点儿的太多了
  + 所以 Node 为了你方便，同时在每一个模块中都提供了一个成员叫：`exports`
  + `exports === module.exports` 结果为  `true`s
  + 所以对于：`moudle.exports.xxx = xxx` 的方式 完全可以：`expots.xxx = xxx`
  + 当一个模块需要导出单个成员的时候，这个时候必须使用：`module.exports = xxx` 的方式
  + 不要使用 `exports = xxx` 不管用
  + 因为每个模块最终向外 `return` 的是 `module.exports`
  + 而 `exports` 只是 `module.exports` 的一个引用
  + 所以即便你为 `exports = xx` 重新赋值，也不会影响 `module.exports`
  + 但是有一种赋值方式比较特殊：`exports = module.exports` 这个用来重新建立引用关系的
  + 之所以让大家明白这个道理，是希望可以更灵活的去用它
```javascript
moudle.exports = {
  a: 123
}

// 重新建立 exports 和 module.exports 之间的引用关系
exports = module.exports

exports.foo = 'bar'
```

### 模块中导出多个成员和导出单个成员
- 模块中导出单个成员和导出多个成员的方式
  + `module.exports = xxx`
  + 通过多次：`exports.xxx = xxx`
  + 导出多个也可以：`moudle.exports = {多个成员}`
- module.exports 和 exports 的区别
  + exports 只是 module.exports 的一个引用而已，目的只是为了简化写法
  + 每个模块最终 return 的是 module.exports

### require 方法加载规则
 - require 方法加载规则
  + 优先从缓存加载
  + 核心模块
  + 路径形式的模块
    * `./xxx`
    * `../xxxx`
    * `/xxxx` / 在这里表示的是磁盘根路径
    * `c:/xxx`
  + 第三方模块
    * 第三方模块的标识就是第三方模块的名称（不可能有第三方模块和核心模块的名字一致）
    * npm
      - 开发人员可以把写好的框架、库发布到 npm 上
      - 使用者在使用的时候就可以很方便的通过 npm 来下载
    * 使用方式：`var 名字 = require('npm install 的那个包名')`
    * node_modules
    * node_modules/express
    * node_modules/express/package.json
    * node_modules/express/package.json main
    * 如果 package.json 或者 package.json main 不成立，则查找备选项：index.js
    * 如果以上条件都不成立，则继续进入上一级目录中的 node_modules 按照上面的规则继续查找
    * 如果直到当前文件模块所属磁盘根目录都找不到，最后报错：`can not find module xxx`

## Node中的其他成员
在每个模块中,除了`require`、`exports`等模块相关AP之外,还有两个特殊的成员:
- `__dirname`**动态获取**可以用来获取当前文件模块所属目录的绝对路径 
- `__filename`**动态获取**可以用来获取当前文件的绝对路径 
- `__dirname`和 `__filename`是不受执行node命令所属路径影响的

在文件操作中,使用相对路径是不可靠的,因为在Node中文件操作的跻径被设计为相对于执行node命令所处的 

路径(不是bug,人家这样设计是有使用场景). 

所了为了解决这个问题,很简单,只需要把相对路径变为绝对路径就可以了. 

那这里我们就可以使用 `__dirname`或者`__filename`来帮我们解决这个问题了 

在拼接路径的过程中,为了避免手动拼接带来的一些低级错误,所以推荐多使用:`path.join()`来辅助拼接. 

所以为了尽量避免刚才所描述这个问题,大家以后在文件操作中使用的相对路径都统一转换为动态的绝对路径
```javascript
app.use('/public', express.static(path.join(__dirname, './public/')))
```


## art-template模板引擎
### 在 Node 中使用 art-template 模板引擎
  + 安装
  ```
  npm install art-template
  ```
  + 加载
  ```javascript
  var template = require('art-template')
  ```
  + 使用
  ```javascript
  template.render('模板字符串', 替换对象)
  ```

### 客户端渲染和服务端渲染的区别
- 服务端渲染和客户端渲染的区别
  + 客户端渲染不利于 SEO 搜索引擎优化
  + 服务端渲染是可以被爬虫抓取到的，客户端异步渲染是很难被爬虫抓取到的
  + 所以你会发现真正的网站既不是纯异步也不是纯服务端渲染出来的
  + 而是两者结合来做的
  + 例如京东的商品列表就采用的是服务端渲染，目的了为了 SEO 搜索引擎优化
  + 而它的商品评论列表为了用户体验，而且也不需要 SEO 优化，所以采用是客户端渲染
  + 最少两次请求，发起 ajax 在客户端使用模板引擎渲染
  + 客户端拿到的就是服务端已经渲染好的
- 处理留言本案例首页数据列表渲染展示
- 处理留言本案例发表留言功能
  + 路径
  + 设计好的请求路径
  + $GET 直接或查询字符串数据
  + Node 中需要咱们自己动手来解析
    * url.parse()
  + /pinglun?name=jack&message=hello
  + split('?')
  + name=jack&message=hello
  + split('&')
  + name=jack message=hello
  + forEach()
  + name=jack.split('=')
  + 0 key
  + 1 value
- 掌握如何解析请求路径中的查询字符串
  + url.parse()
- 如何在 Node 中实现服务器重定向
  + header('location')
    * 301 永久重定向 浏览器会记住
      - a.com b.com
      - a 浏览器不会请求 a 了
      - 直接去跳到 b 了
    * 302 临时重定向 浏览器不记忆
      - a.com b.com
      - a.com 还会请求 a
      - a 告诉浏览器你往 b
- Node 中的 Console（REPL）使用

### art-template中的include-extend-block语法


## npm包管理服务
### package.json 包描述文件
  + 我们建议每一个项目都要有一个package.json文件（包描述文件，就像产品的说明书一样），给人踏实的感觉这个文件可以通过`npm init`的方式来自动初始化出来。
  + 建议每个项目的根目录下都有一个 package.json文件
  + 建议执行`npm install`包名的的时候都加上`--save`这个选项，目的是用来保存依赖项信息
  + dependencies 选项的作用
    * 对于咱们目前来讲，最有用的是那个dependencies选项，可以用来帮我们保存第三方包的依赖信息。
    * 如果你的node_ modules删除了也不用担心，我们只需要：`npm insta1l`就会自动把package.json中的dependencies中所有的依赖项都下载回来。

### npm 常用命令
  - npm init
    + npm init-y可以跳过向导，快速生成
  - npm install
    一次性把 dependencies选项中的依赖项全部安装
    + npm i
  - npm install包名
    只下载
    + npm i包名
  - npm instal-save包名
    + 下载并且保存依赖项（ package json文件中的 dependencies选项）
    + npm i-S包名
  - npm uninstall|包名
    + 只删除，如果有依赖项会依然保存
    + npm un包名
  - npm uninstall-save包名
    + 删除的同时也会把依赖信息也去除
    + npm un-S包名

## express的使用
### 起步
### 安装
```
npm install --save express
```
### hello world
```javascript
const express = require('express')
const app = express()

app.get('/', (req, res) => res.send('Hello World'))

app.listen(3000, () => console.log('Example app listening on port 3000'))
```
### 基本路由
路由器
- 请求方法

- 请求路径

- 请求处理函数

get:

```javascript
//当你以GET方法请求/的时候，执行对应的处理函数
app.get('/', function (req, res) {
	res.send('hello world')
})
```
post:

```javascript
//当你以POST方法请求/的时候，执行对应的处理函数
app.post('/', function (req, res) {
	res.send('Got a POST request')
})
```
### 静态服务
```javascript
//当以/pub1ic/开头的时候，去，pub1ic/目录中找找对应的资源
//这种方式更容易辨识，推荐这种方式
app.use('/public/', express.static('./public/'))
//	/pub1ic资源
app.use(express.static('public'))
//  /files资源
app.use(express.static('files'))
app.use('/static', express.static('public'))
app.use('/static', express.static(path.join(__dirname, 'public')))
```
### 在express中配置`art-template`模板引擎
安装：
```
npm install --save art-template
npm install --save express-art-template
```
配置
```javascript
var express = require(express)

var app = express()

app.use('/public/', express.static('/public/'))

//配置使用art-template模板引擎

//第一个参数，表示，当渲染以.art结尾的文件的时候，使用ant-template模板引擎

// express-art-temp1ate是专门用来在 Express中把art-template整合到 Express中

//虽然外面这里不需要记载 art-template但是也必须安装

//原因就在于 express-art-temp1ate依赖了art- template

//app.engine('art',require('express-art-template'))

app.engine('html',require('express-art-template'))

// Express为 Response相应对象提供了一个方法： render

// render方法默认是不可以使用，但是如果配置了模板引擎就可以使用了

//res.render('htm模板名',{模板数据})
//第一个参数不能写路径,默认会去项目中的 VIeWs目录查找该模板文
//也就是说 Express有一个约定:开发人员把所有的视图文件都放到 VIeWs目录中

//如果想要修改默认的 VIews目录,则可以 

//app.set('VieWs', render函数的默认路径)

app. get('/', function (req, res){
	res render('404.htm1')
})



app.engine('html',require('express-art-template'))
```

使用
```javascript
app. get('/', function (req, res){
	//express 默认会去项目的views目录找index.html
	res render('404.htm1', {
		title: 'hellow world'
	})
})
```
如果希望修改默认的`views`视图渲染目录，可以，
```javascript
注意第一个参数views千万不要写错
app.set('views', 目录路径)
```

### 在express中获取表单GET请求参数
Express内置了一个API，可以直接通过`req.query`来获取
```javascript
req.query
```

### 在Express获取表单POST请求体数据
在 Express中没有内置获取表单POST请求体的API,这里我们需要使用一个第三方包:body-parser

安装
```
nmp install --save body-parser
```
配置
```javascript
var express = require('express') 
//0.引包 
var bodyParser = require('body-parser') 
var app= express()

//配置body-parser中间件(插件,专门用来解析表单PosT请求体)
// parse application/x-Www-form-urlencodec 
//只要加入这个配置,则在req请求对象上会多出来一个属性:body 
//也就是说你就可以直接通过req.body来获取表单PosT请求体数据了

app.use(bodyParser.urlencoded({extended: false })) 
// parse application/ison 
app.use(bodyParser.json()) 

```
使用：
```javascript
app.use(function(reg, res) {

	res.setHeader('Content-Type',text/plain) 

	res.write('you posted:\n') 

	res.end(JSON.stringify (req.body, null, 2))
})
```

## Express - crud

### 模块化思想
模块如何划分：
+ 模块职责要单一

### 起步

- 初始化
- 模板处理

### 路由设计

| 请求方法 | 请求路径         | get  参数 | post  参数                     | 备注         |
| :------- | :--------------- | :-------- | :----------------------------- | ------------ |
| GET      | /studens         |           |                                | 渲染首页     |
| GET      | /students/new    |           |                                | 渲染添加学生 |
| POST     | /studens/new     |           | name，age，gender，hobbies     | 处理添加学生 |
| GET      | /students/edit   | id        |                                | 渲染编辑页面 |
| POST     | /studens/edit    |           | id，name，age，gender，hobbies | 处理编辑请求 |
| GET      | /students/delete | id        |                                | 处理删除请求 |

### 提取路由模块
router.js
```javascript
/*router.js路由模块

职责：

处理路由

根据不同的请求方法+请求路径设置具体的请求处理函数

模块职责要单一，不要乱写

我们划分模块的目的就是为了增强项目代码的可维护性

提升开发效率*/
var express = require('express')

// 1.创建一个路由容器

var router = express.Router()

// 2.把路由都挂载到router路由容器中

router.get('/students', function (req, res) {

})

// 3. 把router导出

module.exports = router 

```
app.js
```javascript
var router = require('./router')

// 挂载路由
app.use(router)
```

### 自己编写的步骤

- 处理模板 
- 配置开放静态资源 
- 配置模板引擎 
- 简单路由:/ students渲染静态页出来 
- 路由设计 
- 提取路由模块 
- 由于接下来一些列的业务操作都需要处理文件数据,所以我们需要封装 student. js 
- 先写好 student. js文件结构
  + 查询所有学生列表的API find
  + findByid
  + save
  + updateByid
  + deleteByid
- 实现具体功能
  + 通过路由收到请求
  + 接收请求中的数据（get，post）
    * req.query
    * req.body
  + 调用数据操作API处理数据
  + 根据操作结果给客户端发送响应

## 在Express配置使用`express-session`插件
__参考文档:https://github.com/expressjs/session__
安装：
```shell
npm install express-session
```
配置：
```javascript
app.use(session({
	secret: 'keyboard cat', //配置加密字符串,它会在原有加密基础之上和这个字符串拼起来去加密目的是为了增加安全性,防止客户端恶意伪造
	resave: false,
	saveUninitialized: true //无论你是否使用 Session,我都默认直接给你分配一把钥匙
}))
```
使用：
```javascript
//添加 Session 数据
req.session.foo = 'bar'

//获取 Session 数据
req.session.foo
```
提示:默认 Session数据是內存存储的,服务器一旦重启就会丢失,真正的生产环境会把 Session进行持久化存储

## Mongod 数据库
链接：
```shell
# 该命令默认链接本机的MongoDB服务
mongo
```
退出
```shell
# 在链接转态输入 exit 退出链接
exit
```

### 在Node中如何操作MongoDB数据
#### 使用官方的MongoDB包
  https://github.com/mongodb/node-mongodb-native
#### 使用第三方mongoose来操作MongoDB数据库
第三方包:`mongoose`基于 MongoDB官方的 `mongodb`包再一次做了封装.
- 网址http://mongoosejs.com
- 官网:http://mongoosejs.com

- 官方指南:http/mongoosejs.com/docs/guide.html 

- 官方AP文档:http://mongoosejs.com/docs/api.html

#### 起步
安装：
```shell
npm i mongoose
```
hello world:
```javascript
var mongoose = require('mongoose');

mongoose.connect('mongodb://localhost/test', {useNewUrlParser: true});

var Kitten = mongoose.model('Kitten', {name: String});

var fluffy = new Kitten({ name: 'fluffy' });

fluffy.save(function (err, fluffy) {
    if (err) return console.error(err);
    fluffy.speak();
  });
```
#### Mongoose的使用
```javascript
var mongoose = require('mongoose')

var Schema = mongoose.Schema

// 1. 连接数据库
// 指定连接的数据库不需要存在，当你插入第一条数据之后就会自动被创建出来
mongoose.connect('mongodb://localhost/itcast')

// 2. 设计文档结构（表结构）
// 字段名称就是表结构中的属性名称
// 约束的目的是为了保证数据的完整性，不要有脏数据
var userSchema = new Schema({
  username: {
    type: String,
    required: true // 必须有
  },
  password: {
    type: String,
    required: true
  },
  email: {
    type: String
  }
})

// 3. 将文档结构发布为模型
//    mongoose.model 方法就是用来将一个架构发布为 model
//    第一个参数：传入一个大写名词单数字符串用来表示你的数据库名称
//                 mongoose 会自动将大写名词的字符串生成 小写复数 的集合名称
//                 例如这里的 User 最终会变为 users 集合名称
//    第二个参数：架构 Schema
//   
//    返回值：模型构造函数
var User = mongoose.model('User', userSchema)


// 4. 当我们有了模型构造函数之后，就可以使用这个构造函数对 users 集合中的数据为所欲为了（增删改查）
// **********************
// #region /新增数据
// **********************
var admin = new User({
  username: 'zs',
  password: '123456',
  email: 'admin@admin.com'
})

admin.save(function (err, ret) {
  if (err) {
    console.log('保存失败')
  } else {
    console.log('保存成功')
    console.log(ret)
  }
})
// **********************
// #endregion /新增数据
// **********************




// **********************
// #region /查询数据
// **********************
User.find(function (err, ret) {
  if (err) {
    console.log('查询失败')
  } else {
    console.log(ret)
  }
})

User.find({
  username: 'zs'
}, function (err, ret) {
  if (err) {
    console.log('查询失败')
  } else {
    console.log(ret)
  }
})

User.findOne({
  username: 'zs'
}, function (err, ret) {
  if (err) {
    console.log('查询失败')
  } else {
    console.log(ret)
  }
})
// **********************
// #endregion /查询数据
// **********************



// **********************
// #region /删除数据
// **********************
User.remove({
  username: 'zs'
}, function (err, ret) {
  if (err) {
    console.log('删除失败')
  } else {
    console.log('删除成功')
    console.log(ret)
  }
})
// **********************
// #endregion /删除数据
// **********************


// **********************
// #region /更新数据
// **********************
User.findByIdAndUpdate('5a001b23d219eb00c8581184', {
  password: '123'
}, function (err, ret) {
  if (err) {
    console.log('更新失败')
  } else {
    console.log('更新成功')
  }
})
// **********************
// #endregion /更新数据
// **********************
```
## 使用Node操作MySQL数据库
安装：
```shell
npm i mysql
```
示例
```javascript
var mysql = require('mysql');

// 1. 创建连接
var connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'root',
  database: 'users' // 对不起，我一不小心把数据库名字和表名起成一样的，你知道就行
});

// 2. 连接数据库 打开冰箱门
connection.connect();

// 3. 执行数据操作 把大象放到冰箱
connection.query('SELECT * FROM `users`', function (error, results, fields) {
  if (error) throw error;
  console.log('The solution is: ', results);
});

// connection.query('INSERT INTO users VALUES(NULL, "admin", "123456")', function (error, results, fields) {
//   if (error) throw error;
//   console.log('The solution is: ', results);
// });

// 4. 关闭连接 关闭冰箱门
connection.end();
```

