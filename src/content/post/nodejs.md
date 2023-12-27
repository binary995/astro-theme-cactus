---
title: "node.js笔记"
publishDate: "2023 9 13"
description: "nodejs笔记包含mongodb"
tags: ["vue3", "note", "nodeJs"]
ogImage: "/social-card.png"
---


#  入门





## 命令行常用操作



|     说明     |   操作    |
| :----------: | :-------: |
|   切换盘符   | C:     D: |
| 切换工作目录 |    cd     |
| 查看目录文件 |    dir    |









## Buffer

>一段固定长度的内存空间,用于处理二进制数据



### 1.特点

1. 大小固定无法调整
2. 性能好,可以直接对计算机内存进行操作
3. 每个元素的大小为一字节



```js
//1.alloc
let buf_1 = Buffer.alloc(10)
// <Buffer 00 00 00 00 00 00 00 00 00 00>

// 2. allocUnsafe
let  buf_2 = Buffer.allocUnsafe(10)
// <Buffer 00 00 00 00 00 00 00 00 00 00>


// 3.from
let buf_3 = Buffer.from('hello')
// <Buffer 68 65 6c 6c 6f>
```



1. `Buffer.alloc(10)`

   创建一个长度为10的Buffer,相当于申请了10字节的内存空间,每个字节的值为0

2. `Buffer.allocUnsafe`

   创建一个长度为10的Buffer,但是Buffer中原本存在的数据不会被重置,所以叫`Unsafe`

3. `Buffer.from`

   创建Buffer,可以通过字符串或者数组



### 2. 操作



#### 1.Buffer转字符串

```js
// buffer转字符串

let buf_4 =  Buffer.from([105,108,111,118,101,121,111,117])

// iloveyou
```







## fs模块

> `file system`可以实现与硬盘的交互



### 1. 文件写入

| 方法                      | 说明     |
| ------------------------- | -------- |
| writeFile                 | 异步写入 |
| writeFileSync             | 同步写入 |
| appendFile/appendFileSync | 追加写入 |
| createWriteStream         | 流式写入 |







1. `fs.writeFile`(异步写入)

   ```js
   // 1.导入 fs 模块
   const fs = require('fs')
   
   // 2.写入文件
   fs.writeFile('./座右铭.txt',' 三人行必有我师焉',err=>{
     // err 写入失败:错误对象   写入成功:null
     if(err){
       console.log('写入失败');
     }else{
       console.log('写入成功');
     }
   })
   ```

   语法: `fs.writeFile(file,data[,options],callback)`

   参数说明:

   - file:文件名
   - data:写入数据
   - options:选项设置
   - callback:写入回调

   

 

2. `writeFileSync`(同步写入)

   ```js
     fs.writeFileSync('./data.txt','test')
   //没有回调
   ```

   

3. `appendFile/appendFileSync`(追加写入)

   ```js
   // 引入fs
   const fs = require('fs')
   
   fs.appendFileSync('./座右铭.txt','这是插入内容')
   
   
    三人行必有我师焉这是插入内容
   ```

   

4. `createWriteStream`(流式写入)

   ```js
   // 1.引入fs
   const fs = require('fs')
   
   //创建曹操.txt
   const ws = fs.createWriteStream('./曹操')
   
   // 写入 write
   ws.write('这是第一段')
   ws.write('第二段')
   
   
   // 关闭通道
   ws.close();
   
   
   这是第一段第二段 
   ```

   适用于大文件写入或者频繁写入的场景,`writeFile`适用于写入频率较低的场景





### 2.文件读取

| 方法             | 说明     |
| ---------------- | -------- |
| readFile         | 异步读取 |
| readFileSync     | 同步读取 |
| createReadStream | 流式读取 |



1. readFile异步读取

   ```js
   const fs=require('fs')
   
   // 异步读取
   fs.readFile('./座右铭.txt',(err,data)=>{
     if(err){
       console.log('读取失败');
       return;
     }
     console.log(data.toString());
   })
   
   
   // 同步读取
   let data_1 = fs.readFileSync('./座右铭.txt')
   console.log(data_1.toString());
   ```

2. readFileSync(同步读取)

   ```js
   // 同步读取
   let data_1 = fs.readFileSync('./座右铭.txt')
   console.log(data_1.toString());
   ```

3. createReadStream(流式读取)

   ```js
   // 流式读取
   
   // 1.创建读取流式对象
   const rs = fs.createReadStream('./座右铭.txt')
   
   // 2.绑定data事件,chunk 块
   rs.on('data',chunk=>{
     console.log(chunk.length); // 65536字节,64Kb
   })
   
   // 3.end  可选事件
   rs.on('end',()=>{
     console.log('读取完成');
   })
   ```

   



### 3.练习

- 将一段mp4文件复制到资料文件夹下面

  

  方式一   `readFile`

  ```js
  const fs = require('fs')
  
  //读取文件
  let data = readFileSync('./mp4');
  
  //写入文件
  fs.writeFileSync('../资料',data);
  ```

  

  方式二  流式操作

  ```js
  //流式操作
  
  const fs= require('fs')
  // 1.读
  const rs = fs.createReadStream('./座右铭.txt')
  // 2.写
  const ws = fs.createWriteStream('./hello.js')
  
  // 绑定data事件
  // 读取的时候需要帮定data事件
  rs.on('data',chunk=>{
    ws.write(chunk)
  })   
  ```

  



### 4.文件重命名和移动



1. 文件重命名

   ```js
    // 1.导入fs模块
   const fs = require('fs');
   
   // 2.重命名
   fs.rename('./座右铭-1','座右铭.txt',err=>{
     if(err){
       console.log('失败');
       return;
     }
     
     console.log('ok');
   })
   ```

   

2. 文件移动

   > 将曹操移动到资料里面,但是资料里面的文件路径要和曹操一致

   ```js
   // 1.文件移动
   fs.rename('./曹操.txt','./资料/曹操.txt',err=>{
     if(err){
       log('false');
       return;
     }
     log('ok')
   }
   )
   ```

   1. `fs.renameSync`效果基本一致,没有回调函数





### 5.文件删除

`fs.unlink`

`fs.unlinkSync`



1. `unlink`方法

```js
const { log } = require('console');
const fs = require('fs');


fs.unlink('./资料/曹操.txt',err=>{
  if(err){
    log('false')
  }
  log('ok')
})
```



2. `rm`方法

   ```js
   const { log } = require('console');
   const fs = require('fs');
   
   
   fs.rm('./资料/曹操.txt',err=>{
     if(err){
       log('false')
     }
     log('ok')
   })
   ```

   





### 6.文件夹操作

1. 创建文件夹

   > `mkdir`

   ```js
   const { log } = require('console');
   const fs = require('fs');
   
   // 创建文件夹操作
   fs.mkdir('./html',err=>{
     if(err){
       log('false');
     }
     log('ok')
   })
   ```

   

2. 递归创建

   > 需要开启属性`{recursive:true}`

   ```js
   // 递归创建
   fs.mkdir('./a/b/c',{recursive:true},err=>{
     if(err){
       log('false');
     }
     log('ok')
   })
   ```

3. 删除文件夹

   `rmdir`

   ```js
   fs.rmdir('./html',err=>{
     if(err){
       log('false');
     }
     log('ok')
   }
   )
   ```

   



### 7.查看资源状态

1. `state`

   ```js
   fs.stat('./作文.txt',(data,err)=>{
     if(err){
       log('false');
       return;
     }
     log(data)
   })
   ```

2. `isFile` 检测目标是否是文件

3. `isDirectory`检测目标是否是文件夹







### 8.路径

1. 相对路径

   ```js
   fs.writeFileSync('./index.html','love')
   ```

2. 绝对路径

   



**注意:**fs模块的相对路径参照物:命令行的工作目录





### 10.重命名练习

```js
const fs = require('fs');

// 1.读取文件中的数据名字
let filesName = fs.readdirSync('./code')

// 2.对读取到的数据进行遍历
filesName.forEach(item=>{
  // 拆分
  let data = item.split('-');
  //拿到数据
  let [num,name] = data;
  // 对数字进行判断
  if(Number(num)<10){
    num = '0' + num;
  }
  // 拼接成新的数据
  let newName = num +'-'+name;
  fs.renameSync(`./code/${item}`,`./code/${newName}`)
})
```





## path模块

|      Api      |           说明           |
| :-----------: | :----------------------: |
| path.resolve  |    拼接规范的绝对路径    |
|   path.sep    | 获取操作系统的路径分隔符 |
|  path.parse   |    解析路径并返回对象    |
| path.basename |    获取路径的基础名称    |
| path.dirname  |     获取路径的目录名     |
| path.extname  |     获得路径的拓展名     |





## Http协议

> 超文本传输 协议



### 1.请求报文

| 方法        | 作用     |
| ----------- | -------- |
| GET         | 获取数据 |
| POST        | 新增数据 |
| PUT / PATCH | 更新数据 |
| DELETE      | 删除数据 |

![image-20230625161345823](https://img1.imgtp.com/2023/07/04/CgbzhrY2.png)





### 2.响应报文

![](https://img1.imgtp.com/2023/07/04/cAfQMQJg.png)



1. 响应状态码

   

| 状态码 | 含义           |
| ------ | -------------- |
| 200    | 请求成功       |
| 403    | 禁止请求       |
| 404    | 找不到资源     |
| 500    | 服务器内部错误 |

| 状态码 | 含义           |
| ------ | -------------- |
| 1XX    | 信息响应       |
| 2XX    | 成功响应       |
| 3XX    | 重定向响应     |
| 4XX    | 客户端错误响应 |
| 5XX    | 服务端错误响应 |





## HTTP模块



### 1. 创建HTTP服务

```js
// 1.导入模块
const http = require('http');

// 2.创建服务对象
const serve = http.createServer((request,response)=>{
  response.end('hello HTTP'); // 设置响应体
})

// 3.监听端口,启动服务
serve.listen(9000,()=>{
  console.log('服务启动');
})
```





**注意:**

1. 命令行 ctrl + c 停止服务
2. 更新代码后,必须重启服务才有效









### 2. 获取请求头或请求行

> 　　想要获得请求的数据，需要通`request`对象





1. 获得http请求报文

   ![image-20230625203410853](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230625203410853.png)

- **注意事项:**
  1. request.url只能获得路径以及查询字符串,无法获得url中的域名以及协议的对象
  2. headers将请求信息转换成一个对象,并将属性名都转换成了小写





```js
// 1.引入http模块
const http = require('http');


// 2.创建服务对象
const serve = http.createServer((request,response)=>{
  // 获得请求方法    需要放在回调函数中
  console.log(request.method);
  // 获得请求的url
  console.log(request.url);
  response.end('HTTP'); //设置响应体
})

// 3.开启监听端口 9000
serve.listen(9000,()=>{
  console.log('服务启动');
})

```





### 3.获得请求体

```js
// 1.引入模块
const http = require('http');

// 2.创建服务对象
const serve = http.createServer((request,response)=>{

  // 1.声明一个变量用来接受数据
  let body = '';
  // 2.绑定data事件
  request.on('data',chunk=>{
    // 这里的chunk就是每次读取的一点内容
    body+=chunk;
  });

  // 3.绑定end事件
  request.on('end',()=>{
    console.log(body);

    // 响应
    response.end('Hello HTTP');
  });
})


// 3.监听端口,启动服务
serve.listen(9000,()=>{
  console.log('端口已启动');
});
```

这样就可以拿到请求报文中的请求体内容





### 4.获得请求路径和查询字符串

```js
//路径
const { log } = require('console');
const http = require('http');

// 1.导入url模块
const url = require('url');

const serve = http.createServer((request,response)=>{
  // 2.解析request.url
  let res = url.parse(request.url);
  // 路径
  let pathName = res.pathname;

  response.end('url');
})


serve.listen(9000,()=>{
  log('启动成功')
});



//字符串
const { log } = require('console');
const http = require('http');

// 1.导入url模块
const url = require('url');

const serve = http.createServer((request,response)=>{
  // 2.解析request.url
  let res = url.parse(request.url);
  
  // 查询字符串
  let keyWord = res.query.keyWord;
  response.end('url');
})


serve.listen(9000,()=>{
  log('启动成功')
});
```







### 5.练习

完成下面要求的HTTP服务

| 请求类型 | 请求地址 | 响应体结果 |
| -------- | -------- | ---------- |
| get      | /login   | login      |
| get      | /reg     | reg        |



```js
const { log } = require('console');
const http = require('http');


const serve = http.createServer((request,response)=>{

  let method = request.method;
  log(method);
  // 获得请求的url路径
  let {pathname} = new URL(request.url,'http://127.0.0.1');
  log(pathname);
  if(method === 'GET' && pathname ==='/login'){
    response.end('Login');
  }else if(method === 'GET' && pathname === '/reg'){
    response.end('Reg');
  }else{
    response.end('404');
  }
})


serve.listen(9000,()=>{

  log('监听启动完毕....')
})
```





### 6.设置HTTP响应报文

![image-20230627160357716](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230627160357716.png)

![image-20230627160409239](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230627160409239.png)

```js
const http = require('http');

const serve = http.createServer((requist,response)=>{

  // 1.设置响应状态码
  response.statusCode= '203';
  // 2.设置状态描述
  response.statusMessage= 'i love you'
  // 3.设置响应头
  response.setHeader('content-type','text/html;charset=utf-8');
  // 4.设置响应体
  response.end('响应报文');
});

serve.listen(9000,()=>{
  console.log('启动监听');
})
```



### 7.练习

搭建HTTP服务,响应一个**4行3列**的表格,并且要求表格要有**隔行换色**效果,且**点击**单元格能**高亮**显示



**js模块**

```js
const http = require('http');
const fs = require('fs');
const serve = http.createServer((requset,response)=>{
  let html = fs.readFileSync('./8-响应练习.html');
  response.end(html);
})

serve.listen(9000,()=>{
  console.log('监听成功...');
})
```

**html模块**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<style>
  table{
    border: 1px solid #000;
    border-collapse: collapse;
  }
  td{
    width: 60px;
    height: 30px;
    border: 1px solid #000;
    text-align: center;
    line-height: 30px;
  }
  tr:nth-child(2n){
    background-color: aqua;
  }
  tr:nth-child(2n+1){
    background-color: skyblue;
  }
</style>
<body>
  <table>
    <tr>
      <td>姓名</td>
      <td>成绩</td>
      <td>学号</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </table>

  <script>
    let tds = document.querySelectorAll('td');
    tds.forEach(item => {
      item.onclick = function(){
        this.style.background = '#222';
      }
    });
  </script>
</body>
</html>
```



### 8-页面外资源的引入

>例子：将css和js代码作为单独的文件进行引入,不能直接在html页面中进行嵌套引入

因为响应题都是通过

```js
const serve = http.createServer((requset,response)=>{
  let html = fs.readFileSync('./8-响应练习.html');
  response.end(html);
})
```

这段代码执行的



所以我们需要根据浏览器的请求路径返回对应的响应体

```js
const http = require('http');
const fs = require('fs');
const serve = http.createServer((requset,response)=>{
  let {pathname} = new URL(requset.url);
    //返回html
  if(pathname === '/'){
    let html = fs.readFileSync(__dirname+'/index.html')
    response.end(html);
      //返回css
  }else if(pathname === '/index.css'){
    let css = fs.readFileSync(__dirname+'/index.css');
    response.end(css);
      //返回js
  }else if(pathname === '/index.js'){
    let js = fs.readFileSync(__dirname+'/index.js');
    response.end(js)
  }else{
    response.end(<h1>404 NOT FOUND</h1>)
  }
})

serve.listen(9000,()=>{
  console.log('监听成功...');
})
```



### 9-搭建静态资源服务

> 如果使用例8,需要对每个请求的pathname都进行判断,在实际操作中太麻烦

```js
const http = require('http');
const fs = require('fs');
```

```js
const serve = http.createServer((requset,response)=>{
  let {pathname} = new URL(requset.url);
  // 拼接文件路径
  let filename = __dirname + pathname;
  // 读取对应文件路径里面的资源
  fs.readFile(filename,(data,err)=>{
    if(err){
      response.statusCode=500;
      response.end('Not Found');
      return;
    }
    response.end(data)
  })

})

```

```js
serve.listen(9000,()=>{
  console.log('监听成功...');
})
```



通过拼接文件路径名的方法,就可以跟方便的管理静态资源





### 10-GET和POST请求  

 

**区别:**

1. 作用:

   - GET请求:主要用于获取数据
   - POST请求:主要用于提交数据

   

2. 参数位置:

   - GET请求:带参请求将参数缀到url中
   - POST请求:带参数请求是将参数放到请求体中

   

3. 安全性:

   POST请求相当于安全一些



4. GET请求大小有限制,一般为2k左右,POST则没有限制











## 模块化

 

### 1.数据暴露

1. module.exorts = value
2. exports.name = value

```js
module.exports = {};
exports = module.exports = {}

exports.name 相当于往{}中添加数据
```



### 2.导入

1. **导入文件**

   - 利用`require`来导入,可以直接使用相对路径;
   - 导入json和js文件可以省略后缀

2. 导入文件夹

   - 如果导入的路径是一个文件夹,则会首先检测该文件夹下的`package.json`文件中的`main`属性对应的文件,存在就会导入,否则报错

   - 如果main属性不存在,或者package.json不存在,则会尝试导入文件夹中的`index.js`或者`index.json`,如果还是没找到,就会报错

     



3. **导入模块的基本流程**
   1. 将相对路径转换为绝对路径,定位目标文件
   2. 缓存检测
   3. 读取目标文件代码
   4. 包裹为一个函数并执行(自执行函数).通过`arguments,callee.toString()`查看自执行函数
   5. 缓存模块的值
   6. 返回`module.exports`的值





### 

## 包管理工具

`npm`是Node的包管理工具



### npm

#### 1.初始化包

创建一个空目录,一起目录作为工作目录`启动命令行工具`,执行`npm init`

**注意:**

- package name(包名) 不能是用中文.大写
  - 使用`npm init -y`或者`npm init --yes`极速创建`package.json`





#### 2.下载包

使用`npm i`或者`npm install`

   

- 在安装完成之后,会增加两个资源

  - node_modules 文件夹,存放下载的包
  - package_lock.json 用来锁定包的版本

  





#### 3.require导入npm包的基本场景

1. 在当前文件夹下node_modules中寻找同名的文件夹,若找不到

   

2. 在上级目录中下的node_modules中寻找同名的问名的文件夹,直至找到磁盘根目录





#### 4.生产环境与开发环境

开发环境:只专门用来写代码的环境,一般指程序员的电脑

生产环境:指项目代码正式运行的环境,一般指正式的服务器电脑

![image-20230701105310554](https://img1.imgtp.com/2023/07/04/t2z2BsAX.png)





#### 5.npm全局安装

```
npm i -g nodemon
```

说明:

- 全局安装的命令不受工作目录位置影响
- 可通过`npm root -g`查看全局安装包的位置





#### 6.修改windows执行策略

1. 以管理员身份打开`powershell`
2. 输入`set-ExecutionPolicy remoteSigned`
3. 选全是





#### 7.环境变量 Path

1. 系统设置
2. 环境变量
3. 在path中添加你想全局打开的文件路径



#### 8.删除依赖

```
##局部删除
npm remove unip

##全局删除
npm remove -g 
```



#### 9.配置别名

![image-20230701111828049.png](https://img1.imgtp.com/2023/07/04/KpsseTQr.png)



- 更改这个serve的文件路径

```js
npm run serve
//运行这个文件路径
```





### cnpm

#### 10.cnpm镜像

1. 安装

   ```js
    npm install -g cnpm --registry=http://registry.npmmirror.com
   ```

2. 配置

   - 直接配置

     ```js
      npm install -g cnpm --registry=http://registry.npmmirror.com
     ```

   - 工具配置

     - 安装nrm

       ```js
       npm i -g nrm
       ```

       

     - 修改镜像

       ```js
       nrm use taobao
       ```

       



### yarn

1. 安装

   ```js
   npm i -g yarn
   ```

2. 常用命令

   - ![image-20230701155401451.png](https://img1.imgtp.com/2023/07/04/VeJkmnKT.png)



3. yarn和npm的选择

- yarn也可以配置淘宝镜像
- 都可以使用,根据实际工作中使用的工具进行选择





### 包管理工具拓展

![image-20230701160523073.png](https://img1.imgtp.com/2023/07/04/dNsTumz9.png)



### nvm

> 管理node版本的工具,方便切换不同版本的node.js

1. 下载

   ```
   https://github.com/coreybutler/nvm-windows/releases
   ```

2. 安装

   ```
   nvm-setup.exe 
   ```

   



3. 常用命令

   - ![image-20230701161711253.png](https://img1.imgtp.com/2023/07/04/1QiNztr5.png)







## Express框架

> 是一个封装好的工具包,封装了很多功能,便于WEB开发



### 请求和响应

**request** 和 **response** 对象的具体介绍：



**Request 对象** - request 对象表示 HTTP 请求，包含了请求查询字符串，参数，内容，HTTP 头部等属性。常见属性有：

1. req.app：当callback为外部文件时，用req.app访问express的实例
2. req.baseUrl：获取路由当前安装的URL路径
3. req.body / req.cookies：获得「请求主体」/ Cookies
4. req.fresh / req.stale：判断请求是否还「新鲜」
5. req.hostname / req.ip：获取主机名和IP地址
6. req.originalUrl：获取原始请求URL
7. req.params：获取路由的parameters
8. req.path：获取请求路径
9. req.protocol：获取协议类型
10. req.query：获取URL的查询参数串
11. req.route：获取当前匹配的路由
12. req.subdomains：获取子域名
13. req.accepts()：检查可接受的请求的文档类型
14. req.acceptsCharsets / req.acceptsEncodings / req.acceptsLanguages：返回指定字符集的第一个可接受字符编码
15. req.get()：获取指定的HTTP请求头
16. req.is()：判断请求头Content-Type的MIME类型
17. 

**Response 对象** - response 对象表示 HTTP 响应，即在接收到请求时向客户端发送的 HTTP 响应数据。常见属性有：

1. res.app：同req.app一样
2. res.append()：追加指定HTTP头
3. res.set()在res.append()后将重置之前设置的头
4. res.cookie(name，value [，option])：设置Cookie
5. opition: domain / expires / httpOnly / maxAge / path / secure / signed
6. res.clearCookie()：清除Cookie
7. res.download()：传送指定路径的文件
8. res.get()：返回指定的HTTP头
9. res.json()：传送JSON响应
10. res.jsonp()：传送JSONP响应
11. res.location()：只设置响应的Location HTTP头，不设置状态码或者close response
12. res.redirect()：设置响应的Location HTTP头，并且设置状态码302
13. res.render(view,[locals],callback)：渲染一个view，同时向callback传递渲染后的字符串，如果在渲染过程中有错误发生next(err)将会被自动调用。callback将会被传入一个可能发生的错误以及渲染后的页面，这样就不会自动输出了。
14. res.send()：传送HTTP响应
15. res.sendFile(path [，options] [，fn])：传送指定路径的文件 -会自动根据文件extension设定Content-Type
16. res.set()：设置HTTP头，传入object可以一次设置多个头
17. res.status()：设置HTTP状态码
18. res.type()：设置Content-Type的MIME类型





### 1.路由

> 　　确定了应用程序如何响应客户端对特定端口的请求



#### **1.1:组成**

- 请求方法 (get,post....)
- 路径
- 回调函数



```js
//app.<method>(path,callback)

app.get('/home',(req,res)=>{
    res.end('home首页')
})
```

使用过程

- 请求require
- 创建对象
- 创建路由
- 监听端口

```js
// 导入express
const express = require('express');

// 创建应用对象
const app = express();

// 创建路由
app.get((req,res)=>{
  res.end('home首页');
});

app.post('/',(req,res)=>{
  res.end('hello');
});

// 所有请求方式
app.all('test',(req,res)=>{
  res.end('test');
})

// 任意方案,任意路径
app.all('*',(req,res)=>{
  res.end('**')
})
// 监听端口
app.listen(3000,()=>{
  console.log('启动');
})
```



#### 1.2 获得请求参数



获得参数请求基本和http一致

```js
const express = require('express')


const app = express();

app.get('/register',(req,res)=>{
  // 请求方法
  console.log(req.method);
  // 请求路径
  console.log(req.url);
  // http版本号
  console.log(req.httpVersion);
  // 请求头
  console.log(req.headers);
  //......
})

app.listen(3000,()=>{
  console.log('启动');
})


```



#### 1.3获取路由参数

```js
req.params.'name'
```

```js
const express = require('express')


const app = express();

// :id是占位符,可以是任何其他值
app.get('/:id',(req,res)=>{
  // params是req提供的方法,获得req中所有的路由参数,id是上面的占位符
  console.log(req.params.id);
  
})

app.listen(3000,()=>{
  console.log('启动');
})


```



#### 练习

根据路由参数响应歌手的信息

```
/singer/1.html
```

```js
const express = require('express')
// 引入json文件
const singer = require('./singer.json')

const app = express();

// :id是占位符,可以是任何其他值
app.get('/:id',(req,res)=>{
  // 获得路由参数
  const id = req.params.id;
  // 在数组中找到对应的id
  let result = singer.find(item=>{
    if(item.id === Number(id)){
      return true;
    }
  });
  console.log(result);
  
})

app.listen(3000,()=>{
  console.log('启动');
})


```





### 2.express响应

```js
const express = require('express')


// :id是占位符,可以是任何其他值
app.get('/:id',(req,res)=>{
  //原生响应
  res.statusCode = 404;
  res.statusMessage = 'love';
  res.setHeader('xxx','yyy');
  res.write('hello express');
  res.end('response');
   
    
  //express响应
  // 响应码
  res.status(500);
  // 响应头
  res.set('aaa','bbb');
  //响应体  使用express框架send时,使用中文是不会乱码的
  res.send('你好啊')

})

app.listen(3000,()=>{
  console.log('启动');
})


```



- **express响应方法**

  ```js
  1. status() // 响应状态码
  
  2. set('xxx','yyy')  //响应头
  
  3. send('响应体')  
  
  4. 连贯操作   res.status(404).set('xxx','yyy').send('这是响应体')
  
  5. res.redirect('http://')   // 重定向
  
  6. res.download('./package.json')  //下载响应
  
  7. res.json(); //响应 JSON
  
  8.res.sendFile(__dirname + '/home.html') //响应文件内容
  ```

  ```js
  app.get('/other',(req,res)=>{
    // 跳转响应
    res.redirect('http://www.baidu.com');
    // 下载响应
    res.download(__dirname+'package.json');
    // JSON响应
    res.json({
      name:'小工',
      slogon:'你在干嘛'
    })
  })
  ```





 



### express中间件

> 中间件本质是一个回调函数
>
> 中间件函数可以像路由回调一样访问`请求对象(req)`,`响应对象(res)`



![image-20230708151112128](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708151112128.png)

#### 全局中间件



##### 声明中间件函数

```js
let recordMiddleware = function(request,response,next){
  //实现功能代码
  //.....
  //执行next函数(当如果希望执行完中间件函数之后，仍然继续执行路由中的回调函数，必须调用next)
  next();
}
```



##### 使用中间件

```js
app.use(recordMiddleware)
```

**也可不声明,直接将匿名函数传递给use**

```js
app.use(function(req,next,res){
   console.log('这是第一个中间件');
    next();
});
```

**多个中间件**

express允许使用app.use()定义多个全局中间件

```js
app.use(function (request, response, next) {
  	console.log('定义第一个中间件');
    next();
})
app.use(function (request, response, next) {
  	console.log('定义第二个中间件');
    next();
})
```







##### 中间件的类型

- 全局中间件
- 路由中间件







  

##### 2.练习

```js
//记录每个请求的url和ip地址


//记录每个请求的url和ip地址

const express = require('express');
const path = require('path');
const fs = require('fs');


const app = express();
// 声明中间件
function recordMiddleware(req,res,next){
  // 获得url和ip
  let {ip,url} = req;
  fs.appendFileSync(path.resolve(__dirname+'/access.log'),`${url} ${ip} \r\n`)
  next();
};

app.use(recordMiddleware);



app.get('/home',(req,res)=>{
  // 使用解构赋值来拿到Url和ip
  res.send('home组件')
})

app.get('/admin',(req,res)=>{
  res.send('admin')
})

app.get('*',(req,res)=>{
  res.send("全局")
})


app.listen(3000,()=>{
  console.log("3000端口监听中");
})
```



**步骤**

1. 声明中间件函数
2. 使用中间件
3. 全局中间件在每个请求都会自动调用





#### 路由中间件

如果只需要对某一些路由进行功能封装,这就需要路由中间件

```js
//非封装
app.get('/admin',(req,res)=>{
    if(req.query.code === '521'){
      res.send('ok');
    }else{
      res.send('false');
    }
});

app.get('/路径','中间件函数1','中间件函数2',(req,res)=>{
    
})
```

![image-20230708151054395](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708151054395.png)

![image-20230704151027093](https://gitee.com/xiaocaiji_learn/img-bed/raw/master/img/image-20230704151027093.png)



路由中间件可以看作路由里面添加的判断函数



```js
//封装
function checkCodeMiddleware(req,next,res){
    if(req.params,code === '521'){
        next();
    }else{
        res,.send('false')
    }
}

//使用
app.get('/admin',checkCodeMiddleware,(req,res)=>{
      res.send('false');
});
```

```js
const express = require('express');

const app = express();

// 中间件
function checkCodeMiddleware(req,res,next){
  if(req.query.code === '521'){
    next();
  }else{
    res.send('false')
  }
};

app.get('/home',checkCodeMiddleware,(req,res)=>{
  res.send('首页')
})

app.listen(3000,()=>{
  console.log('监听中');
  
})

```





### 静态资源中间件

express处理内置静态资源的中间件

```js
//引入express框架
const express = require('express');
//创建服务对象
const app = express();
//静态资源中间件的设置，将当前文件夹下的public目录作为网站的根目录
app.use(express.static('./public')); //当然这个目录中都是一些静态资源
//如果访问的内容经常变化，还是需要设置路由
//但是，在这里有一个问题，如果public目录下有index.html文件，单独也有index.html的路由
//则谁书写在前，优先执行谁
app.get('/index.html',(request,response)=>{
	respsonse.send('首页');
});
//监听端口
app.listen(3000,()=>{
	console.log('3000 端口启动....');
});
```

```js
//静态资源中间件的设置，将当前文件夹下的public目录作为网站的根目录
app.use(express.static('./public')); //当然这个目录中都是一些静态资源
```

获得public文件夹中的静态资源,会访问对应的静态资源(例如html页面)



> 注意事项:
>
> 1. index.html 文件为默认打开的资源
> 2. 如果静态资源与路由规则同时匹配，谁先匹配谁就响应
> 3. 路由响应动态资源，静态资源中间件响应静态资源





### 获得请求体数据 body-parser

![image-20230708151046204](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708151046204.png)

```js
const urlParser = bodyParser.urlParser.urlencoded({extended:false});
const jsonParser = bodyParser.json();
```







### 防盗链

>  防止外部网站盗用网站资源

![image-20230708151029100](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708151029100.png)

`referer`返回不同的域名和端口,服务器通过判断是否为本地域名来决定返回内容







Eg:

只允许127.0.0.1访问

```js
const express = require('express');

const app = express();

// 只允许127.0.0.1访问img


// 声明中间件
app.use((req,res,next)=>{
  // 检测请求头中的referer是否为127.0.0.1
  let referer = req.get('referer'); //log   http://127.0.0.1:3000

  // 将域名提取出来,就是不包含http:// 和端口号的域名内容  127.0.0.1
 if(referer){
   // 实例化
   let url = new URL(referer);
   // 获取hostname
   let hostname = url.hostname;
   // 判断
   if(hostname !== '127.0.0.1'){
     res.status(404).send('404 Not Found');
     return;
   }
 }
  next();

})

```





### 路由模块化

```js
const express = require('express');

// 创建路由对象
const router = express.Router();

// 创建路由规则
//创建路由

router.get('/home',(req,res)=>{
  res.send('home首页');
})


router.post('/register',(req,res)=>{
  res.send('register首页');
})

// 对外暴露 router
module.exports = router;
```

在总router文件中应用

```js
const express = require('express');

// 导入
const router = require('./路由模块化-1')

const app = express();

// 使用
app.use(router);

```







### EJS模板引擎

#### 安装

```js
 npm install ejs
```



#### 用法

```js
let ejs = require('ejs'),
    people = ['geddy', 'neil', 'alex'],
    html = ejs.render('<%= people.join(", "); %>', {people: people});
```



#### 标签含义

- `<%` '脚本' 标签，用于流程控制，无输出。
- `<%_` 删除其前面的空格符
- `<%=` 输出数据到模板（输出是转义 HTML 标签）
- `<%-` 输出非转义的数据到模板
- `<%#` 注释标签，不执行、不输出内容
- `<%%` 输出字符串 '<%'
- `%>` 一般结束标签
- `-%>` 删除紧随其后的换行符
- `_%>` 将结束标签后面的空格符删除



#### 参数

- `cache` 缓存编译后的函数，需要指定 `filename`
- `filename` 被 `cache` 参数用做键值，同时也用于 include 语句
- `context` 函数执行时的上下文环境
- `compileDebug` 当值为 `false` 时不编译调试语句
- `client` 返回独立的编译后的函数
- `delimiter` 放在角括号中的字符，用于标记标签的开与闭
- `debug` 将生成的函数体输出
- `_with` 是否使用 `with() {}` 结构。如果值为 `false`，所有局部数据将存储在 `locals` 对象上。
- `localsName` 如果不使用 `with` ，localsName 将作为存储局部变量的对象的名称。默认名称是 `locals`
- `rmWhitespace` 删除所有可安全删除的空白字符，包括开始与结尾处的空格。对于所有标签来说，它提供了一个更安全版本的 `-%>` 标签（在一行的中间并不会剔除标签后面的换行符)。
- `escape` 为 `<%=` 结构设置对应的转义（escape）函数。它被用于输出结果以及在生成的客户端函数中通过 `.toString()` 输出。(默认转义 XML)。
- `outputFunctionName` 设置为代表函数名的字符串（例如 `'echo'` 或 `'print'`）时，将输出脚本标签之间应该输出的内容。
- `async` 当值为 `true` 时，EJS 将使用异步函数进行渲染。（依赖于 JS 运行环境对 async/await 是否支持）





[EJS -- 嵌入式 JavaScript 模板引擎 | EJS 中文文档 (bootcss.com)](https://ejs.bootcss.com/#install)



















## mongoDB

> 基于分布式文件存储的数据库

###   服务器安装

[4-Linux服务器部署MongoDB_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1vd4y1t7q9/?spm_id_from=333.337.search-card.all.click)

### 核心概念

![image-20230708151019913](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708151019913.png)





### 下载安装与启动

下载地址： [https://www.mongodb.com/try/download/community](https://gitee.com/link?target=https%3A%2F%2Fwww.mongodb.com%2Ftry%2Fdownload%2Fcommunity)

建议选择 `zip` 类型， 通用性更强

配置步骤如下:

 1> 将压缩包移动到 `C:\Program Files` 下，然后解压

 2> 创建 `C:\data\db` 目录，mongodb 会将数据默认保存在这个文件夹

 3> 以 mongodb 中 bin 目录作为工作目录，启动命令行

 4> 运行命令 `mongod`

![image-20230708151009494](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708151009494.png)

看到最后的 `waiting for connections` 则表明服务 `已经启动成功`

然后可以使用 `mongo` 命令连接本机的 mongodb 服务

![image-20230708151001474](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708151001474.png)

> 注意：
>
> - 为了方便后续方便使用 mongod 命令，可以将 bin 目录配置到环境变量 Path 中
> - `千万不要选中服务端窗口的内容`，选中会停止服务，可以 `敲回车` 取消选中

配置windows服务: [https://www.shuzhiduo.com/A/E35pAE6Ydv/](https://gitee.com/link?target=https%3A%2F%2Fwww.shuzhiduo.com%2FA%2FE35pAE6Ydv%2F)

修改`mongodb`数据库位置

![image-20230327010148369](https://gitee.com/xiaocaiji_learn/img-bed/raw/master/img/image-20230327010148369.png)

### 命令行交互

命令行交互一般是学习数据库的第一步，不过这些命令在后续用的比较少，所以大家了解即可

### 数据库命令

1. 显示所有的数据库

   ```
   show dbs
   ```

2. 切换到指定的数据库，如果数据库不存在会自动创建数据库

   ```
   use 数据库名
   ```

3. 显示当前所在的数据库

   ```
   db
   ```

4. 删除当前数据库

   ```
   use 库名
   db.dropDatabase()
   ```

### 集合命令

1. 创建集合

   ```
   db.createCollection('集合名称')
   ```

2. 显示当前数据库中的所有集合

   ```
   show collections
   ```

3. 删除某个集合

   ```
   db.集合名.drop()
   ```

4. 重命名集合

   ```
   db.集合名.renameCollection('newName')
   ```

### 文档命令

1. 插入文档

   ```
   db.集合名.insert(文档对象);
   ```

2. 查询文档

   ```
   db.集合名.find(查询条件)
   ```

   _id 是 mongodb 自动生成的唯一编号，用来唯一标识文档

3. 更新文档

   ```
   db.集合名.update(查询条件,新的文档)
   db.集合名.update({name:'张三'},{$set:{age:19}})
   ```

4. 删除文档

   ```
   db.集合名.remove(查询条件)
   ```

### 应用场景

#### 新增

- 用户注册
- 发布视频
- 发布商品
- 发朋友圈
- 发评论
- 发微博
- 发弹幕
- .......

#### 删除

- 删除评论
- 删除商品
- 删除文章
- 删除视频
- 删除微博
- ......

#### 更新

- 更新个人信息
- 修改商品价格
- 修改文章内容
- ......

#### 查询

- 商品列表
- 视频列表
- 朋友圈列表
- 微博列表
- 搜索功能
- ......

## Mongoose

### 介绍

Mongoose 是一个对象文档模型库，官网 [http://www.mongoosejs.net/](https://gitee.com/link?target=http%3A%2F%2Fwww.mongoosejs.net%2F)

- 可以让我们通过Node来操作MongoDB数据库的模块

  ![image-20231005212146907](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20231005212146907.png)

### 作用

方便使用代码操作 mongodb 数据库



### new Object

- Schema(模式对象)

  定义约束了数据库中的文档结构

- Model

  作为集合中的所有文档的表示,相当于MongoDB中的`collection`



```js
const mongoose = require('mongoose')

//连接数据库
mongoose.connect('mongodb://127.0.0.1:27017/bilibili')


//连接成功回调
mongoose.connection.on('open',()=>{
  console.log('连接成功');
})

//错误回调
mongoose.connection.on('error',()=>{
  console.log("错误");
})

//关闭回调
mongoose.connection.on('close',()=>{
  console.log('连接关闭');
})

//关闭
setTimeout(()=>{
  mongoose.disconnect()
},5000)
```





### 使用流程

```
 //1. 安装 mongoose
 //2. 导入 mongoose
 const mongoose = require('mongoose');

 //3. 连接数据库                              数据库名称
 mongoose.connect('mongodb://127.0.0.1:27017/bilibili');

 //4. 设置连接回调
 //连接成功   once 一次   事件回调函数只执行一次
mongoose.connection.on('open', () => {
	console.log('连接成功');
 	//5. 创建文档结构对象
 	
    // 设置集合中 文档的属性以及属性值得类型
 	let BookSchema = new mongoose.Schema({
 		title: String,
 		author: String,
 		price: Number
 	});
     
 	//6. 创建文档模型对象  对文档操作的封装对象  mongoose会使用集合名称的复数，创建集合
 	let BookModel = mongoose.model('book', BookSchema);
	
     //7. 插入文档
 	BookModel.create({
 		title: '西游记',
 		author: '吴承恩',
		price: 19.9
 	}, (err, data) => {
        // 判断是否有错误
 		if (err) throw err;
        //输出 data 对象  如果没有出错，则输出插入后的文档对象
 		console.log(data);
 		//8. 断开连接  关闭数据链接 (项目运行过程中，不会添加该代码)
 		mongoose.disconnect();
 	});
});

//连接出错
mongoose.connection.on('error', () => {
	console.log('连接出错~~');
})

//连接关闭
mongoose.connection.on('close', () => {
	console.log('连接关闭');
})
```

### 字段类型

文档结构可选的常用字段类型列表

| 类型       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| String     | 字符串                                                       |
| Number     | 数字                                                         |
| Boolean    | 布尔值                                                       |
| Array      | 数组，也可以使用 `[]` 来标识                                 |
| Date       | 日期                                                         |
| Buffer     | Buffer 对象                                                  |
| Mixed      | 任意类型，需要使用 `mongoose.Schema.Types.Mixed` 指定        |
| ObjectId   | 对象 ID，需要使用 `mongoose.Schema.Types.ObjectId` 指定      |
| Decimal128 | 高精度数字，需要使用 `mongoose.Schema.Types.Decimal128` 指定 |

### 字段值验证

Mongoose 有一些内建验证器，可以对字段值进行验证

#### 必填项

```
title: {
	type: String,
 	required: true // 设置必填项  该属性不能为空
},
```

#### 默认值

```
author: {
 	type: String,
 	default: '匿名' //默认值
},
```

#### 枚举值

```
gender: {
	type: String,
	enum: ['男','女'] //设置的值必须是数组中的
},
```

#### 唯一值

```
username: {
	type: String,
 	unique: true
},
```

> unique 需要 `重建集合` 才能有效果
>
> 永远不要相信用户的输入

### CURD

数据库的基本操作包括四个，增加（create），删除（delete），修改（update），查（read）

#### 增加

插入一条

```
SongModel.create({
	title:'给我一首歌的时间',
 	author: 'Jay'
}, function(err, data){
	//错误
	console.log(err);
	//插入后的数据对象
	console.log(data);
});
```

批量插入

```js
//1.引入mongoose
const mongoose = require('mongoose');

//2.链接mongodb数据库 connect 连接
mongoose.connect('mongodb://127.0.0.1:27017/project');

//3.设置连接的回调
mongoose.connection.on('open',()=>{
	//4.声明文档结构
	const PhoneSchema = new mongoose.Schema({
		brand:String,
		color:String,
		price:Number,
 		tags:Array
	})
	
	
 	//6.创建模型对象
 	const PhoneModel = mongoose.model('phone',PhoneSchema);
 	//新增
 	PhoneModel.insertMany([
 		{
 			brand:'华为',
 			color:'灰色',
 			price:2399,
 			tags:['电量大','屏幕大','信号好']
 		},
 		{
 			brand:'小米',
 			color:'白色',
 			price:2099,
 			tags:['电量大','屏幕大','信号好']
         }
	],(err,data)=>{
		if(err) throw err;
         console.log('写入成功');
         mongoose.connection.close();
	})
})
```

#### 删除

删除一条数据

```
SongModel.deleteOne({_id:'5dd65f32be6401035cb5b1ed'}, function(err){
 	if(err) throw err;
 	console.log('删除成功');
	mongoose.connection.close();
});
```

批量删除

```
SongModel.deleteMany({author:'Jay'}, function(err){
 	if(err) throw err;
 	console.log('删除成功');
	mongoose.connection.close();
});
```

#### 更新

更新一条数据

```
SongModel.updateOne({author: 'JJ Lin'}, {author: '林俊杰'}, function (err) {
	if(err) throw err;
	mongoose.connection.close();
});
```

批量更新数据

```
SongModel.updateMany({author: 'Leehom Wang'}, {author: '王力宏'}, function (err) {
	if(err) throw err;
	mongoose.connection.close();
});
```

#### 查询

查询一条数据

```
SongModel.findOne({author: '王力宏'}, function(err, data){
	if(err) throw err;
	console.log(data);
	mongoose.connection.close();
});
//根据 id 查询数据
SongModel.findById('5dd662b5381fc316b44ce167',function(err, data){
	if(err) throw err;
	console.log(data);
	mongoose.connection.close();
});
```

批量查询数据

```
//不加条件查询
SongModel.find(function(err, data){
	if(err) throw err;
	console.log(data);
	mongoose.connection.close();
});
//加条件查询
SongModel.find({author: '王力宏'}, function(err, data){
	if(err) throw err;
	console.log(data);
	mongoose.connection.close();
});
```

### 条件控制

#### 运算符

在 mongodb 不能 > < >= <= !== 等运算符，需要使用替代符号

- `>`使用 `$gt`
- `<` 使用 `$lt`
- `=` 使用 `$gte`
- `<=` 使用 `$lte`
- `!==` 使用 `$ne`

```
db.students.find({id:{$gt:3}}); id号比3大的所有的记录

// 价格小于 20 的图书
BookModel.find({ price: { $lt: 20 } }, (err, data) => {
	if (err) {
		console.log('读取失败~~~')
		return
    }
    console.log(data)
})
```

#### 逻辑运算

`$or` 逻辑或的情况

```
db.students.find({$or:[{age:18},{age:24}]});

// 曹雪芹 或者 余华的书
BookModel.find({ $or: [{ author: '曹雪芹' }, { author: '余华' }] }, (err, data)=>{
    if (err) {
      console.log('读取失败~~~')
      return
    }
    console.log(data)
})
```

`$and` 逻辑与的情况

```
db.students.find({$and: [{age: {$lt:20}}, {age: {$gt: 15}}]});

// 价格大于 30 且 小于 70
BookModel.find({ $and: [{ price: { $gt: 30 } }, { price: { $lt: 70 } }] }, (err, data) => {
    if (err) {
      console.log('读取失败~~~')
      return
    }
    console.log(data)
})
```

#### 正则匹配

条件中可以直接使用 JS 的正则语法，通过正则可以进行模糊查询

```
db.students.find({name:/imissyou/});

正则表达式，搜索书籍名称中带有 '三' 的图书
BookModel.find({ name: /三/ }, (err, data) => {
	if (err) {
		console.log('读取失败~~~')
      	return
	}
   console.log(data)
})

BookModel.find({ name: new RegExp('三') }, (err, data) => {
 	if (err) {
      	console.log('读取失败~~~')
      	return
    }
    console.log(data)
})
```

### 个性化读取

#### 字段筛选

```
//0:不要的字段
//1:要的字段
SongModel.find().select({_id:0,title:1}).exec(function(err,data){
	if(err) throw err;
	console.log(data);
	mongoose.connection.close();
});
```

#### 数据排序

```
// sort 排序
// 1:升序
// -1:倒序
SongModel.find().sort({hot:1}).exec(function(err,data){
	if(err) throw err;
	console.log(data);
	mongoose.connection.close();
});
```

#### 数据截取

```
//skip 跳过   limit 限定
SongModel.find().skip(10).limit(10).exec(function(err,data){
	if(err) throw err;
	console.log(data);
	mongoose.connection.close();
});
```

## 图形化管理工具

我们可以使用图形化的管理工具来对 Mongodb 进行交互，这里演示两个图形化工具

- Robo 3T 免费 [https://github.com/Studio3T/robomongo/releases](https://gitee.com/link?target=https%3A%2F%2Fgithub.com%2FStudio3T%2Frobomongo%2Freleases)
- Navicat 收费 [https://www.navicat.com.cn/](https://gitee.com/link?target=https%3A%2F%2Fwww.navicat.com.cn%2F)





##  Mongoose代码模块化

**总代码**

```js
const mongoose = require('mongoose')

//连接数据库
mongoose.connect('mongodb://127.0.0.1:27017/bilibili')


//连接成功回调
mongoose.connection.on('open',()=>{
  console.log('连接成功');

  let BooksSchema =  new mongoose.Schema({
    name:String,
    author:String,
    price:Number
  })

  
  let BooksModel = mongoose.model('books',BooksSchema)


增
BooksModel.insertMany([{
  name:'001',
  author:'001-1',
  price:20
},
{
  name:'002',
  author:'001-1',
  price:20
},
{
  name:'003',
  author:'001-1',
  price:20
},
{
  name:'004',
  author:'001-1',
  price:20
},
]).then((data,err)=>{
  if(err){
    console.log(err);
  }
})

删
BooksModel.deleteMany({name:'001'}).then((data,err)=>{
  if(err){
    console.log(err);
  }
})
//错误回调
mongoose.connection.on('error',()=>{
  console.log("错误");
})

//关闭回调
mongoose.connection.on('close',()=>{
  console.log('连接关闭');
})

```



1. 创建db文件夹用来存放模块
   - db
     - db.js         存放数据库连接,回调等基本操作,通过封装给不同模块使用
     - modules        封装的Model和Schema



db.js          

```js
// 将这些代码当成一个函数暴露出去,再在其他的代码中进行引用
// success 是成功的回调函数,error是失败的回调函数,具体函数需传递过来

module.exports  = function(success,error){

  //进行判断是否为error , 为error()设置默认值
if(typeof error !== 'function'){
  error = () =>{
    console.log('连接失败');
  }
}

const mongoose = require('mongoose')

  //连接服务器
mongoose.connect('mongodb://127.0.0.1:27017/bilibili')


// 成功回调
mongoose.connection.on('open',()=>{
  success()
})

// 失败回调
mongoose.connection.on('close',()=>{
  error()
})

}
```



model.js    BookModel.js

```js
const mongoose = require('mongoose')


const BooksSchema = new mongoose.Schema({
  name:String,
  author:String,
  price:Number
})

const BooksModel = mongoose.model('book',BooksSchema)

//对外暴露

module.exports = BooksModel
```





index.js               具体操作,写在success函数中

```js
//将db.js引入,在js中进行调用
const db =  require('./db/db')
//引入mongoose
const mongoose = require('mongoose')
//引入Model模型
const BooksModel = require('./models/BookModel')

//调用函数,相当于启动服务   里面是success和error回调
db(()=>{

  console.log('连接成功');
  //连接成功 success函数
 //具体操作,Schema和Model已经封装在models中
 BooksModel.insertMany([
  {
    name:'代码模块化',
    author:'001',
    price:530
  }
 ])

},
)

```









##  接口

### 简介

### 接口是什么

> 接口是 前后端通信的桥梁

简单理解：一个接口就是 服务中的一个路由规则 ，根据请求响应结果

接口的英文单词是 API (Application Program Interface)，所以有时也称之为 `API 接口`

这里的接口指的是『数据接口』， 与编程语言（Java，Go 等）中的接口语法不同

### 接口的作用

> 实现 前后端通信

![image-20230708150945927](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150945927.png)

### 接口的开发与调用

大多数接口都是由 后端工程师 开发的， 开发语言不限

一般情况下接口都是由 前端工程师 调用的，但有时 后端工程师也会调用接口 ，比如短信接口，支付接口 等

### 接口的组成

一个接口一般由如下几个部分组成

- 请求方法
- 接口地址（URL）
- 请求参数
- 响应结果

一个接口示例 [https://www.free-api.com/doc/325](https://gitee.com/link?target=https%3A%2F%2Fwww.free-api.com%2Fdoc%2F325)

体验一下： [https://api.asilu.com/idcard/?id=371522199111299668](https://gitee.com/link?target=https%3A%2F%2Fapi.asilu.com%2Fidcard%2F%3Fid%3D371522199111299668)

## RESTful API

RESTful API 是一种特殊风格的接口，主要特点有如下几个：

- URL 中的路径表示 资源，路径中不能有 `动词`，例如`create` , `delete` , `update` 等这些都不能有
- 操作资源要与 `HTTP 请求方法` 对应
- 操作结果要与 `HTTP 响应状态码` 对应

规则示例：

| 操作         | 请求类型 | URL      | 返回                 |
| ------------ | -------- | -------- | -------------------- |
| 新增歌曲     | POST     | /song    | 返回新生成的歌曲信息 |
| 删除歌曲     | DELETE   | /song/10 | 返回一个空文档       |
| 修改歌曲     | PUT      | /song/10 | 返回更新后的歌曲信息 |
| 修改歌曲     | PATCH    | /song/10 | 返回更新后的歌曲信息 |
| 获取所有歌曲 | GET      | /song    | 返回歌曲列表数组     |
| 获取单个歌曲 | GET      | /song/10 | 返回单个歌曲信息     |

> 扩展阅读： [https://www.ruanyifeng.com/blog/2014/05/restful_api.html](https://gitee.com/link?target=https%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2014%2F05%2Frestful_api.html)

## json-server

json-server 本身是一个 JS 编写的工具包，可以快速搭建 RESTful API 服务

官方地址: [https://github.com/typicode/json-server](https://gitee.com/link?target=https%3A%2F%2Fgithub.com%2Ftypicode%2Fjson-server)

操作步骤：

1. 全局安装 `json-server`

   ```
   npm i -g json-server
   ```

2. 创建 JSON 文件（db.json），编写基本结构

   ```
   {
   	"song": [
   		{ "id": 1, "name": "干杯", "singer": "五月天" },
   		{ "id": 2, "name": "当", "singer": "动力火车" },
   		{ "id": 3, "name": "不能说的秘密", "singer": "周杰伦" }
   	]
   }
   ```

3. `以 JSON 文件所在文件夹作为工作目录`，执行如下命令

   ```
   json-server --watch db.json
   ```

默认监听端口为 `3000`

## 接口测试工具

介绍几个接口测试工具 apipost [https://www.apipost.cn/](https://gitee.com/link?target=https%3A%2F%2Fwww.apipost.cn%2F) (中文) apifox [https://www.apifox.cn/](https://gitee.com/link?target=https%3A%2F%2Fwww.apifox.cn%2F) (中文) postman [https://www.postman.com/](https://gitee.com/link?target=https%3A%2F%2Fwww.postman.com%2F) (英文)

















## 记账本案例





### 1.搭建框架和路由

1. 创建文件夹

2. ```
   //创建account文件用来存放框架内容
   express -e account
   ```

3. ```
   //安装npm依赖
   npm i
   ```

4. 如果想使用`nodemon`可以在`package.json`中将`node`改成`nodemon`

5. 路由文件在`router`文件夹中





**router文件**

![image-20230708150934427](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150934427.png)







### 2.响应静态页面

1. 在`view`文件夹添加静态页面

![image-20230708150919480](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150919480.png)

2. 在路由文件夹中使用`render`方法,将页面进行绑定

   ```js
   var express = require('express');
   var router = express.Router();
   
   // 记账本列表
   router.get('/account',(req,res,next)=>{
     res.render('list')
   })
   // 添加记录
   router.get('/account/create',(req,res,next)=>{
     res.render('create')
   })
   
   module.exports = router;
   ```

   



### 3.获得表单数据

1. 完成`views`中的`create.ejs`页面

   ```js
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <title>添加记录</title>
     <style>
       .box {
         margin: 0 auto;
         width: 400px;
         padding: 20px;
         background-color: #f2f2f2;
         border-radius: 5px;
       }
       .textbox {
         margin-bottom: 20px;
       }
       label {
         display: block;
         margin-bottom: 5px;
       }
       input[type="text"],
       select {
         width: 100%;
         height: 30px;
         padding: 5px;
         border: 1px solid #ccc;
         border-radius: 3px;
       }
       button[type="submit"] {
         background-color: skyblue;
         border: none;
         width: 100%;
         height: 40px;
         font-size: 16px;
         color: white;
         cursor: pointer;
       }
     </style>
   </head>
   <body>
     <div class="box">
       <h1>添加记录</h1>
       <hr>
   
       <form action="/account" method="post" onsubmit="return validateForm()">
         <div class="textbox">
           <label for="event"><strong>事件</strong></label>
           <input type="text" id="event" name="event" required>
         </div>
   
         <div class="textbox">
           <label for="time"><strong>时间</strong></label>
           <input type="text" id="time" name="time" required>
         </div>
   
         <div class="textbox">
           <label for="type"><strong>类型</strong></label>
           <select id="type" name="type">
             <option value="支出">支出</option>
             <option value="收入">收入</option>
           </select>
         </div>
   
         <div class="textbox">
           <label for="amount"><strong>金额</strong></label>
           <input type="number" id="amount" name="amount" required>
         </div>
   
         <div class="textbox">
           <label for="remark"><strong>备注</strong></label>
           <input type="text" id="remark" name="remark">
         </div>
   
         <button type="submit">提交</button>
       </form>
     </div>
   
     <script>
       function validateForm() {
         var event = document.getElementById('event').value;
         var time = document.getElementById('time').value;
         var amount = document.getElementById('amount').value;
   
         if (event.trim() === '') {
           alert('请输入事件');
           return false;
         }
   
         if (time.trim() === '') {
           alert('请输入时间');
           return false;
         }
   
         if (isNaN(parseFloat(amount))) {
           alert('请输入有效的金额');
           return false;
         }
   
         return true;
       }
     </script>
   </body>
   </html>
   
   ```

   - 记得给表单元素中的`input`添加`name`





2. 完善`routes`的`index.js`

   ```js
   var express = require('express');
   var router = express.Router();
   
   // 记账本列表
   router.get('/account',(req,res,next)=>{
     res.render('list')
   })
   // 添加记录
   router.get('/account/create',(req,res,next)=>{
     res.render('create')
   })
   
   router.post('/account', (req, res) => {
     console.log(req.body);
     res.send('表单提交成功！');
   });
   module.exports = router;
    
   ```

   ```js
   //提交之后
   router.post('/account', (req, res) => {
     console.log(req.body);
     res.send('表单提交成功！');
   });
   ```

   







### 4.lowdb的使用

- 安装

  ```
  npm i lowdb@1.0.0
  ```

  

- 创建`test`文件夹



- 添加`lowdb.js`

  ```js
  const low = require('lowdb')
  const FileSync = require('lowdb/adapters/FileSync')
   
  const adapter = new FileSync('db.json')
  const db = low(adapter);
  
  
  
  // 初始化数据
  db.defaults({ posts: [], user: {} }).write();
  
  //写入数据
  db.get('posts').push({id:2,title:'天气可以'}).write()  //尾部插入
  db.get('posts').unshift({id:2,title:'天气还可以'}).write() //头部插入
  
  
  //删除数据
  db.get('posts').remove({id:3}).write() //有返回值,接受的返回值是删除的数据
  
  
  //读取数据
  console.log(db.get('posts').value())
  
  //获得单条数据
  let res = db.get('posts'),find({id:1}).value()
  
  //更新数据
  db.get('posts').find({id:3}).assign({title:'今天下雨了'}).write()
  ```

  

- 在`lowdb.js`中运行`npm ./lowdb.js`

  程序会自动创建`db.json`

  ![image-20230708150907869](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150907869.png)



lowdb@1.0.0官网

[lowdb - npm (npmjs.com)](https://www.npmjs.com/package/lowdb/v/1.0.0)











### 5.保存账单数据







1. 创建`data`文件夹并在里面创建`db.json`手动初始化

   ![image-20230708150901886](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150901886.png)

   

2. 将`lowdb`初始化代码放到`routes`的`index`文件夹中(修改json的位置)

   ```js
   const adapter = new FileSync(__dirname+'../data/db.json')
   ```

   

3. 新增记录

   ```js
   router.post('/account', (req, res) => {
    db.get('accounts').push(req.body).write();
   });
   ```

4. 使用`shortid`来添加

   ```js
   npm i shortid
   ```

   - 导入到`index`中
   - 在路由回调中生成id
   - 将id放入





```js
var express = require('express');
var router = express.Router();
const low = require('lowdb')
const FileSync = require('lowdb/adapters/FileSync')
 
const adapter = new FileSync(__dirname+'/../data/db.json')

// 获得db对象
const db = low(adapter);

//导入shortid
const shortid = require('shortid')

// 记账本列表
router.get('/account',(req,res,next)=>{
  res.render('list')
})
// 添加记录
router.get('/account/create',(req,res,next)=>{
  res.render('create')
})

router.post('/account', (req, res) => {
  // 生成id
  let id = shortid.generate();
 db.get('accounts').unshift({id:id,...req.body}).write();
 res.send('添加成功')
});
module.exports = router;
 
```







### 6.完善账单列表

![image-20230709012524629](C:/Users/11219/AppData/Roaming/Typora/typora-user-images/image-20230709012524629.png)

> 现在这个账单详情页面的数据都是静态数据,现在我们想让这些数据变成动态的





- 获取数据

  ```js
  // 记账本列表
  router.get('/account',(req,res,next)=>{
    // 获得数据库中的数据
    let accounts = db.get('accounts').value();
    // 将数据传递到list页面中
    res.render('list',{accounts:accounts})
  })
  ```

  

- 将数据传递到`list`页面

  ```js
  res.render('list',{accounts:accounts})
  ```

  

- 将传递的数据进行动态渲染

  ```js
   <div class="accounts">
        <% accounts.forEach(item=>{ %>
        <div class="box1" >
          <div class="top" style=" background-color: pink;">
            <span><%= item.time %></span>
          </div>
          <div class="bot">
            <span><%= item.event %></span>
            <span class="method" style=" background-color: pink;"><收入></span>
            <span class="price" ><%= item.amount%></span>
            <a href="/account/<%= item.id%>">x</a>
          </div>
        </div>
        <% }) %>
      </div>
  ```

  使用模板语法  利用forEach进行渲染











### 7.删除账单

- 在`router`中的`index.js`中添加删除数据

  ```js
  // 删除数据
  router.get('/account/:id',(req,res)=>{
    // 获得paremas的id参数;
    let id = req.params.id;
    //删除
    db.get('accounts').remove({id:id}).write();
  
  })
  ```

- 在html页面中添加`x`

  ```js
  //点击x完成删除数据
  <a href="/account/<%= item.id%>">x</a>
  ```

  





### 8.结合mongodb数据库

> 将`db.json`中的数据转移到数据库中

Schema格式

```js
//Schema
const accountSchema = mongoose.Schema({
  event:{
    type:String,
    required:true
  },
  time:{
    type:Date,
    required:true
  },
  type:{
    type:String,
    required:true
  },
  amount:{
    type:Number,
    required:true
  },
  remark:{
    type:String,
    required:true
  }
})
```

这里time是日期对象格式,我们使用moment包,将time转换为日期对象

```js
//1.npm i moment

//2 
const moment = require('moment')

//3.将字符串转换为对象,在将对象转换为日期对象
monment('2023-02-23').toDate()
```

























#  会话控制

## 介绍

> 所谓会话控制就是 对会话进行控制

HTTP 是一种无状态的协议，它没有办法区分多次的请求是否来自于同一个客户端，无法区分用户

而产品中又大量存在的这样的需求，所以我们需要通过 会话控制 来解决该问题

常见的会话控制技术有三种：

- cookie
- session
- token

## cookie

### cookie 是什么

> cookie 是 HTTP 服务器发送到用户浏览器并保存在本地的一小块数据

cookie 是保存在浏览器端的一小块数据

cookie 是按照域名划分保存的

简单示例：

| 域名                                                         | cookie                        |
| ------------------------------------------------------------ | ----------------------------- |
| [www.baidu.com](https://gitee.com/link?target=http%3A%2F%2Fwww.baidu.com) | a=100; b=200                  |
| [www.bilibili.com](https://gitee.com/link?target=http%3A%2F%2Fwww.bilibili.com) | xid=1020abce121; hm=112411213 |
| jd.com                                                       | x=100; ocw=12414cce           |

### cookie 的特点

浏览器向服务器发送请求时，会自动将 `当前域名下` 可用的 cookie 设置在请求头中，然后传递给服务器

这个请求头的名字也叫 `cookie` ，所以将 cookie 理解为一个 HTTP 的请求头也是可以的

### cookie 的运行流程

填写账号和密码校验身份，校验通过后下发 cookie

![image-20230708150840749](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150840749.png)

### 浏览器操作 cookie

浏览器操作 cookie 的操作，使用相对较少，大家了解即可

1. 禁用所有 cookie
2. 删除 cookie
3. 查看 cookie

### cookie 的代码操作

express 中可以使用 `cookie-parser` 进行处理

```js
const express =require('express');
//1. 安装 cookie-parser	 npm i cookie-parser
//2. 引入 cookieParser 包
const cookieParser = require('cookie-parser');
  
const app = express();

//3. 设置 cookieParser 中间件
app.use(cookieParser());
  
//4-1 设置 cookie
app.get('/set-cookie', (request, response) => {
	// 不带时效性    会在浏览器关闭的时候，销毁
	response.cookie('username','wangwu');
  	// 带时效性
  	response.cookie('email','23123456@qq.com', {maxAge: 5*60*1000 });
  	//响应
  	response.send('Cookie的设置');
});

//4-2 读取 cookie
app.get('/get-cookie', (request, response) => {
    //读取 cookie
    console.log(request.cookies);
  	//响应体
  	response.send('Cookie的读取');
});

//4-3 删除cookie
app.get('/delete-cookie', (request, response) => {
	//删除
	response.clearCookie('username');
	//响应
	response.send('cookie 的清除');
});

//4. 启动服务
app.listen(3000, () => {
	console.log('服务已经启动....');
});
```

> 不同浏览器中的 cookie 是相互独立的，不共享

## session

### session 是什么

session 是保存在 服务器端的一块儿数据，保存当前访问用户的相关信息

### session 的作用

实现会话控制，可以识别用户的身份，快速获取当前用户的相关信息

### session 运行流程

填写账号和密码校验身份，校验通过后创建 `session 信息`，然后将 `session_id` 的值通过响应头返回给浏览器

![image-20230708150824760](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150824760.png)

### session 的代码操作

express 中可以使用 `express-session` 对 session 进行操作

```js
const express = require('express');
//1. 安装包 npm i express-session connect-mongo
//2. 引入 express-session connect-mongo
const session = require("express-session");
const MongoStore = require('connect-mongo');

const app = express();

//3. 设置 session 的中间件
app.use(session({
	name: 'sid', //设置cookie的name，默认值是：connect.sid
	secret: 'atguigu', //参与加密的字符串（又称签名）  加盐
	saveUninitialized: false, //是否为每次请求都设置一个cookie用来存储session的id
	resave: true, //是否在每次请求时重新保存session
	store: MongoStore.create({
		mongoUrl: 'mongodb://127.0.0.1:27017/project' //数据库的连接配置
	}),
	cookie: {
		httpOnly: true, // 开启后前端无法通过 JS 操作
		maxAge: 1000 * 300 // 这一条 是控制 sessionID 的过期时间的！！！
	},
}))

//创建 session  session的设置
app.get('/login', (req, res) => {
    //设置session
    req.session.username = 'zhangsan';
    req.session.email = 'zhangsan@qq.com'
    res.send('登录成功');
})

//获取 session
app.get('/home', (req, res) => {
    console.log('session的信息');
    console.log(req.session.username);
    // 检测 session 是否存在用户数据
    if (req.session.username) {
    	res.send(`你好 ${req.session.username}`);
    }else{
    	res.send('登录 注册');
    }
})

//销毁 session
app.get('/logout', (req, res) => {
    //销毁session
    // res.send('设置session');
    req.session.destroy(() => {
    	res.send('成功退出');
	});
});
app.listen(3000, () => {
	console.log('服务已经启动, 端口 ' + 3000 + ' 监听中...');
});
```

## session 和 cookie 的区别

cookie 和 session 的区别主要有如下几点：

1. 存在的位置

- cookie：浏览器端
- session：服务端

1. 安全性

- cookie 是以明文的方式存放在客户端的，安全性相对较低
- session 存放于服务器中，所以安全性 `相对` 较好

1. 网络传输量

- cookie 设置内容过多会增大报文体积， 会影响传输效率
- session 数据存储在服务器，只是通过 cookie 传递 id，所以不影响传输效率

1. 存储限制

- 浏览器限制单个 cookie 保存的数据不能超过 `4K` ，且单个域名下的存储数量也有限制
- session 数据存储在服务器中，所以没有这些限制

## token

### token 是什么

```
token` 是服务端生成并返回给 HTTP 客户端的一串加密字符串， `token` 中保存着`用户信息
```

token 不属于 http 标准，完全由前后端协商而定，但 cookie 属于 http 标准

### token 的作用

实现会话控制，可以识别用户的身份，主要用于移动端 APP

### token 的工作流程

填写账号和密码校验身份，校验通过后响应 token，token 一般是在响应体中返回给客户端的

 后续发送请求时，需要`手动`将 token 添加在请求报文中(cookie是自动携带的)，一般是放在请求头中

![image-20230708150752250](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150752250.png) ![image-20230708150804291](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150804291.png)

### token 的特点

- 服务端压力更小
  - 数据存储在客户端
- 相对更安全
  - 数据加密
  - 可以避免 CSRF（跨站请求伪造）
- 扩展性更强
  - 服务间可以共享
  - 增加服务节点更简单

### JWT

JWT（JSON Web Token ）是目前最流行的跨域认证解决方案，可用于基于 `token` 的身份验证

JWT 使 token 的生成与校验更规范

我们可以使用 `jsonwebtoken 包` 来操作 token

```
//导入 jsonwebtokan
const jwt = require('jsonwebtoken');

//创建 token
// jwt.sign(数据, 加密字符串, 配置对象)
let token = jwt.sign({
    username: 'zhangsan'
}, 'atguigu', {
    expiresIn: 60 //单位是 秒
})

//解析 token
// jwt.verify(token,加密字符串，回调函数)
jwt.verify(token, 'atguigu', (err, data) => {
    if(err){
    	console.log('校验失败~~');
	    return
	}
	console.log(data);// { username: '张三', iat: (创建时间), exp:(过期时间)}
})
```

> 扩展阅读： [https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html](https://gitee.com/link?target=https%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2018%2F07%2Fjson_web_token-tutorial.html)

## 附录

### 本地域名

所谓本地域名就是 只能在本机使用的域名，一般在开发阶段使用

#### 操作流程

编辑文件 `C:\Windows\System32\drivers\etc\hosts`

```
127.0.0.1   www.baidu.com
```

如果修改失败，可以修改该文件的权限

![image-20230708150716907](https://raw.githubusercontent.com/binary995/img_beg/main/image-20230708150716907.png)

#### 原理

在地址栏输入 `域名` 之后，浏览器会先进行 DNS（Domain Name System）查询，获取该域名对应的 IP 地 址 请求会发送到 DNS 服务器，可以 `根据域名返回 IP 地址`

可以通过 `ipconfig /all` 查看本机的 DNS 服务器

`hosts` 文件也可以设置域名与 IP 的映射关系，在发送请求前，可以通过该文件获取域名的 IP 地址











# 跨域(cors方案)

1. 安装cors

   ```
   //1.npm
    npm i cors
    
    //2. pnpm  
     pnpm add cors
   ```

2. 在路由配置之前使用cors中间价

   ```js
   const cors = require('cors')
   app.use(cors())
   ```

3. 配置响应头

   

   

   

   - **`Access-Control-Allow-Origin`**

     ![image-20230922094644108](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230922094644108.png)

     指定任何网站都可以访问,使用`*`

     ![image-20230922094740956](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230922094740956.png)





- **`Access-Control-Allow -Headers`** 

  ![image-20230922094931370](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230922094931370.png)





- **`Access-Control-Alow-Methods`**

  ![image-20230922095318480](https://raw.githubusercontent.com/binary995/img_beg/main/Picgo/image-20230922095318480.png)



docker pull harisekhon/hbase:latest





