## 跨域的三种方式：
本文通过三种方式实现跨域，由node.js搭建静态服务器，通过修改host文件的本地地址来实现不同源策略，上面三部分代码分别为各个方式的源码

```
  127.0.0.1    a.com
  127.0.0.1    b.com
```

### 方式一： postMessage
postMessage是HTML5引入的新API：跨文档通信API（Cross-document messaging）；  
此API对象新增了一个`window.postMessage`方法，允许跨窗口通信，无论是否同源；  
可以通过监听`'message'`事件，来监听对方发来的消息，并能通过`message`事件对象的`event`的属性进行一些操作；

> + event.source   //  发送消息的窗口  
> + event.origin   //  消息发向的网址
> + event.data     //  消息内容

#### 具体实现方法：
通过postMessage方法实现左侧父窗口中的input（`http://a.com:8888/index.html`）框和右侧子窗口的input(`http://b.com:8888/b.html`)的框的内容同步显示和修改

![image](https://user-images.githubusercontent.com/24493052/27994286-3db75106-64ed-11e7-92d8-74123bb5ed64.png)


1. 父窗口和子窗口分别通过`postMessage`方法向对方发送消息

```
xxx.postMessage('input.value','接收消息的origin')
```

![image](https://user-images.githubusercontent.com/24493052/27994322-13f00592-64ee-11e7-8d8f-cee6fe11487c.png)

2. 父窗口和子窗口分别通过监听message事件event.data来获取消息内容，对自己的input.value进行同步操作

```
window.addEventListener('message', function (e){
			ipt.value = e.data	
		})
```

### 方式二：JSONP
JSONP是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。
它的基本思想是，网页通过添加一个<script>元素，向服务器请求JSON数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。

![image](https://user-images.githubusercontent.com/24493052/27994481-cf853798-64f1-11e7-9593-f7110005278a.png)

![image](https://user-images.githubusercontent.com/24493052/27994510-2f00d812-64f2-11e7-8ed9-1e852993c8b6.png)

### 方式三：CORS
CORS是一个W3C标准，全称是"跨域资源共享"（Cross-origin resource sharing）。
它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。

#### 本质：
1. 浏览器再向跨源服务器发送请的时候会夹带一个一个`origin`字段标明请求方的地址

```
GET /main.js HTTP/1.1
Host: b.com:8888
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
Origin: http://a.com:8888  //夹带的origin头信息，表明身份

```

2. 但服务器接收的此请求后看到对方夹带的origin信息，这个地址我允许它请求我的数据，所以服务端对此地址进行了友好处理

```
response.setHeader('Access-Control-Allow-Origin', 'http://a.com:8888');  // 对'http://a.com:8888'此地址放行

```

3.浏览器看到`Access-Control-Allow-Origin: http://a.com:8888`后，就认可此操作合法；

![image](https://user-images.githubusercontent.com/24493052/27994647-821fbec6-64f4-11e7-9308-f40426687956.png)

---

+ [阮一峰：浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)

+ [阮一峰：跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
