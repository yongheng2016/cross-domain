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

