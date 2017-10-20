# node.js 用socket实现聊天  
## 服务器搭建    
app.js
```js  
const http = require("http");
const express = require("./express");

//创建一个服务
const server = http.createServer(express);

//监听服务端口
server.listen(9999,()=>{
    console.log("服务端已经启动,请访问 http://localhost:9999");
});  
```    
express.js  
```js  
const url=require("url");
const fs=require("fs");

function express(req,res){
    var urlObj=url.parse(req.url);
    //console.log(urlObj);

    var filePath="./www"+urlObj.pathname;
    var content="not found";
    if(fs.existsSync(filePath)){
        content=fs.readFileSync(filePath);
    }
    
    res.end(content.toString());
}


module.exports=express;  
```  
index.html  
```js  
<!DOCTYPE html>
<html lang="en">
    <head>
      <meta charset="utf-8"/>
        <title>Socket.IO chat</title>
        <style>
          * { margin: 0; padding: 0; box-sizing: border-box; }
          body { font: 13px Helvetica, Arial; }
          form { background: #000; padding: 3px; position: fixed; bottom: 0; width: 100%; }
          form input { border: 0; padding: 10px; width: 90%; margin-right: .5%; }
          form button { width: 9%; background: rgb(130, 224, 255); border: none; padding: 10px; }
          #messages { list-style-type: none; margin: 0; padding: 0; }
          #messages li { padding: 5px 10px; }
          #messages li:nth-child(odd) { background: #eee; }
        </style>
      </head>
      <body>
        <ul id="messages"></ul>
        <form action="">
          <input id="m" autocomplete="off" /><button>Send</button>
        </form>

        <script src="js/lib/jquery-1.11.1.js"></script>
        <script src="js/lib/socket.io.js"></script> 
        <script src="js/index.js"></script>
      </body>
</html>  
```  
## 客户端服务搭建与服务端通信  
我们要建立服务端socket请求连接  
```js  
io.on('connection', function(socket){
    console.log('a user connected');

    //断开连接
    socket.on('disconnect', function(){
        console.log('user disconnected');
    });
});  
```
index.js  
```js  
//客户端建立连接 
var socket = io();  
```  
 
## 客户端向服务端发送请求  
index.js  
```js  
$('form').submit(function(){
    //触发事件
    socket.emit('chat message', $('#m').val());
    $('#m').val('');
    return false;
  });   
```  
app.js
```js
//接收客户端的信息
socket.on('chat message', function(msg){
    console.log('message: ' + msg);
});  
```  
将服务端的数据广播到客户端去  
```js  
socket.on('chat message', function(msg){
        console.log('message: ' + msg);

        socket.broadcast.emit("clientE",msg);
    });  
  ```  
客户端接收服务端广播出来的数据  
```js  
socket.on('clientE', function(msg){
    $('#messages').append($('<li>').text(msg));
});  
```  
