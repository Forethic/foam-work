# 13 RPC 技术

RPC，英文 RangPaCong，中文让爬虫，旨在为爬虫开路，秒杀一切，让爬虫畅通无阻！![图片](https://mmbiz.qpic.cn/mmbiz_png/iabtD4jabia4JzgV5a872xpcP6D7FN77h8KQYxvO1sPqZaFR2F9XwoNLHxuIAWhIpC4o6sfZnKAPviaefVffZnumw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

WebSocket 的出现，使得浏览器具备了实时双向通信的能力。

参考：https://blog.csdn.net/zyym9009/article/details/104203995

参考：https://www.cnblogs.com/chyingp/p/websocket-deep-in.html

## 13.1 什么是 websocket

- WebSocket 是 HTML5 下一种新的协议（websocket 协议本质上是一个基于 tcp 的协议）
- 它实现了浏览器与服务器全双工通信，能更好的节省服务器资源和带宽并达到实时通讯的目的
- Websocket 是一个**持久化**的协议

## 13.2 websocket 的原理

- websocket 约定了一个通信的规范，通过一个握手的机制，客户端和服务器之间能建立一个类似 tcp 的连接，从而方便它们之间的通信
- 在 websocket 出现之前，web 交互一般是基于 http 协议的短连接或者长连接
- websocket 是一种全新的协议，不属于 http 无状态协议，协议名为"ws"

**总结（总体过程）：**

- **首先，客户端发起 http 请求，经过 3 次握手后，建立起 TCP 连接；http 请求里存放 WebSocket 支持的版本号等信息，如：Upgrade、Connection、WebSocket-Version 等；**
- _然后，服务器收到客户端的握手请求后，同样采用 HTTP 协议回馈数据；_
- **最后，客户端收到连接成功的消息后，开始借助于 TCP 传输信道进行全双工通信。**

### 13.2.1 处理方法

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    <input id="box" type="text" />
    <button onclick="ps()">发送</button>

    <script>
      // 与服务器约定的连接 以及端口  本机的   hosts文件  localhost      www.ps.com
      const websocket = new WebSocket("ws://127.0.0.1:8080/");

      //连接发生错误的回调方法
      websocket.onerror = () => {
        console.log("WebSocket连接发生错误");
      };

      //连接成功建立的回调方法
      websocket.onopen = function () {
        console.log("WebSocket连接成功");
      };

      //接收到消息的回调方法  接收服务器的数据
      websocket.onmessage = function (event) {
        console.log(event.data);
      };

      //连接关闭的回调方法
      websocket.onclose = function () {
        console.log("WebSocket连接关闭");
      };

      function ps() {
        // jquery -> val   JS -> value
        var text = document.getElementById("box").value;
        // 客户端发信息发服务器
        websocket.send(text);
      }
    </script>
  </body>
</html>
```

### 13.2.2 服务端

```python
pip install websockets
```

- connect: 在 client 端使用，用于建立连接。

- send:发送数据，server 和 client 双方都可以使用。

- [recv](https://so.csdn.net/so/search?q=recv&spm=1001.2101.3001.7020):接收数据，server 和 client 双方都可以使用。

- close:关闭连接，server 和 client 双方都可以使用。

```python
    # encoding: utf-8
    import asyncio
    import websockets

    async def echo(websocket):
        # 使用WebSocket在客户端和服务器之间建立全双工双向连接后，就可以在连接打开时调用send()方法。
        message = 'hello world'
        # 发送数据
        await websocket.send(message)
        return True

    async def recv_msg(websocket):
        while 1:
            # 接收数据
            recv_text = await websocket.recv()
            print(recv_text)

    async def main_logic(websocket,path):
        await echo(websocket)
        await recv_msg(websocket)

    start_server = websockets.serve(main_logic, '127.0.0.1', 8080)
    loop = asyncio.get_event_loop()
    loop.run_until_complete(start_server)
    # 创建了一个连接对象之后，需要不断监听返回的数据，则调用 run_forever 方法，要保持长连接即可
    loop.run_forever()
```

### 13.2.3 实际使用

地址：

## 13.2 JSRPC 简介

​ **RPC** 技术是非常复杂的，对于我们搞爬虫、逆向的来说，不需要完全了解，只需要知道这项技术如何在逆向中应用就行了。

RPC 在逆向中，简单来说就是将本地和浏览器，看做是服务端和客户端，二者之间通过 `WebSocket` 协议进行 `RPC` 通信，在浏览器中将加密函数暴露出来，在本地直接调用浏览器中对应的加密函数，从而得到加密结果，不必去在意函数具体的执行逻辑，也省去了扣代码、补环境等操作，可以省去大量的逆向调试时间。

官网：https://sekiro.iinti.cn/sekiro-doc/

### 13.2.1 Sekiro-RPC

官网地址：https://sekiro.iinti.cn/sekiro-doc/

#### 13.2.1 sk 食用方式

##### 13.2.1.1 执行方式

在本地开启服务端

- 需要有 Java 环境，配置参考：https://blog.csdn.net/qq_62959684/article/details/122469086

  - JDK 远程地址：链接：https://pan.baidu.com/s/1qWutQEJ7315exPqQzNLspA 提取码：vhfg

- Linux & Mac：`bin/sekiro.sh`

- Windows：`bin/sekiro.bat`

##### 13.2.2.2 客户端环境

- 地址：[file.virjar.com/sekiro*web_client.js?*=123](http://file.virjar.com/sekiro_web_client.js?_=123)

##### 13.2.2.3 参数说明

- 使用原理：客户端注入到浏览器环境，然后通过`SekiroClient`和 `Sekiro`服务器通信，即可直接 `RPC` 调用浏览器内部方法，官方提供的`SekiroClient` 代码样例如下：

```javascript
// 生成唯一标记uuid编号
function guid() {
  function S4() {
    return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
  }
  return (
    S4() +
    S4() +
    "-" +
    S4() +
    "-" +
    S4() +
    "-" +
    S4() +
    "-" +
    S4() +
    S4() +
    S4()
  );
}
// 连接服务端
var client = new SekiroClient(
  "ws://127.0.0.1:5620/business-demo/register?group=ws-group&clientId=" + guid()
);
// 业务接口
client.registerAction("登陆", function (request, resolve, reject) {
  resolve("" + new Date());
});
```

- **group**：业务类型（接口组），每个业务一个 `group`，`group` 下面可以注册多个终端（`SekiroClient`），同时`group` 可以挂载多个 `Action`；

- **clientId**：指代设备，多个设备使用多个机器提供 `API` 服务，提供群控能力和负载均衡能力；

- **SekiroClient**：服务提供者客户端，主要场景为手机/浏览器等。最终的 `Sekiro` 调用会转发到 `SekiroClient`。每个`client` 需要有一个惟一的 `clientId`；

- **registerAction**：接口，同一个 `group` 下面可以有多个接口，分别做不同的功能；
- **resolve**：将内容传回给服务端的方法；
- **request**：服务端传过来的请求，如果请求里有多个参数，可以以键值对的方式从里面提取参数然后再做处理。

#### 13.2.2 测试使用

##### 13.2.2.1 1 新建 HTML 页面

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    <script src="http://file.virjar.com/sekiro_web_client.js?_=123"></script>
    <script>
      function guid() {
        function S4() {
          return (((1 + Math.random()) * 0x10000) | 0)
            .toString(16)
            .substring(1);
        }
        return (
          S4() +
          S4() +
          "-" +
          S4() +
          "-" +
          S4() +
          "-" +
          S4() +
          "-" +
          S4() +
          S4() +
          S4()
        );
      }

      var client = new SekiroClient(
        "ws://127.0.0.1:5620/business-demo/register?group=rpc-test&clientId=" +
          guid()
      );

      client.registerAction("clientTime", function (request, resolve, reject) {
        resolve("" + new Date());
      });
    </script>
  </body>
</html>
```

##### 13.2.2.2 SK API

`Sekiro` 为我们提供了一些 API

- 查看分组列表：http://127.0.0.1:5620/business-demo/groupList

- 查看队列状态：http://127.0.0.1:5620/business-demo/clientQueue?group=test

- 调用转发：http://127.0.0.1:5620/business-demo/invoke?group=test&action=test&param=testparm

##### 13.2.2.3 python 代码调试

```python
# encoding: utf-8
"""
@author: 夏洛
@QQ: 1972386194
@file: 测试.py
"""
import requests
data = {"group": "rpc-test",
        "action": "clientTime",
        }
res = requests.get("http://127.0.0.1:5620/business-demo/invoke",params=data )
print(res.text)
```

#### 13.2.3 油猴注入

##### 13.2.3.0 逆向目标

- 地址：http://q.10jqka.com.cn/

- 接口：http://q.10jqka.com.cn/index/index/board/all/field/zdf/order/desc/page/2/ajax/1/

- 对抗：`cookie`反爬虫处理，关键字`v`

##### 13.2.3.1 找到关键点

```javascript
(function () {
  Object.defineProperty(document, "cookie", {
    set: function (val) {
      if (val.indexOf("v") != -1) {
        debugger;
      }
      console.log("Hook捕获到cookie设置->", val);
      return val;
    },
  });
})();
```

找到关键位置，然后导出生成 cookie 加载的函数

![image-20220829154301457](C:\Users\XL\AppData\Roaming\Typora\typora-user-images\image-20220829154301457.png)

##### 13.2.3.3 连接服务器并转发

```javascript
function guid() {
  function S4() {
    return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
  }
  return (
    S4() +
    S4() +
    "-" +
    S4() +
    "-" +
    S4() +
    "-" +
    S4() +
    "-" +
    S4() +
    S4() +
    S4()
  );
}

var client = new SekiroClient(
  "ws://127.0.0.1:5620/business-demo/register?group=rpc-test&clientId=" + guid()
);

client.registerAction("ths", function (request, resolve, reject) {
  resolve(ss());
});
```

![image-20220829154631871](images\image-20220829154631871.png)

#### 13.2.4 代码注入

暴露 boss 接口数据

```javascript
(function () {
  var newElement = document.createElement("script");
  newElement.setAttribute("type", "text/javascript");
  newElement.setAttribute(
    "src",
    "https://sekiro.virjar.com/sekiro-doc/assets/sekiro_web_client.js"
  );
  document.body.appendChild(newElement);
  function guid() {
    function S4() {
      return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
    }
    return (
      S4() +
      S4() +
      "-" +
      S4() +
      "-" +
      S4() +
      "-" +
      S4() +
      "-" +
      S4() +
      S4() +
      S4()
    );
  }
  function startSekiro() {
    var client = new SekiroClient(
      "ws://127.0.0.1:5620/business-demo/register?group=rpc-demo&clientId=" +
        guid()
    );

    client.registerAction("get", function (request, resolve, reject) {
      e = request["seed"];
      t = request["ts"];
      res = new a().z(
        e,
        parseInt(t) + 60 * (480 + new Date().getTimezoneOffset()) * 1e3
      );
      resolve(res);
    });
  }
  setTimeout(startSekiro, 1000);
})();
```

##### 13.2.4.1 获取动态参数

接口：https://www.zhipin.com/job_detail/

从这个接口里面抓取 seed 和 ts

```python
def get_seed_ts():
    url = f"https://www.zhipin.com/job_detail/"
    headers = {
        "user-agent": f"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/{random.randint(1, 999)}.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36",
    }

    response = requests.get(url, headers=headers )
    query_str = parse.urlparse(response.url).query
    query_dict = {i.split("=")[0]: i.split("=")[1] for i in query_str.split("&")}
    seed = parse.unquote(query_dict.get("seed"))
    ts = query_dict.get("ts")
    return seed,ts
```

##### 13.2.4.2 重新请求 JS 服务端获取数据

```python
# encoding: utf-8
"""
@author: 夏洛
@QQ: 1972386194
@file: rpc_test.py
"""
from urllib import parse
import random
import urllib3,requests
urllib3.disable_warnings()


def get_seed_ts():
    url = f"https://www.zhipin.com/job_detail/"
    headers = {
        "user-agent": f"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/{random.randint(1, 999)}.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36",
    }
    response = requests.get(url, headers=headers )
    query_str = parse.urlparse(response.url).query
    query_dict = {i.split("=")[0]: i.split("=")[1] for i in query_str.split("&")}
    seed = parse.unquote(query_dict.get("seed"))
    ts = query_dict.get("ts")
    return seed,ts

def get_sig():
    seed,ts = get_seed_ts()
    data = {
        "group": "rpc-demo",
        "action": "get",
        'e':seed,
        'i': ts
    }
    res = requests.post(url="http://127.0.0.1:5620/business-demo/invoke", data=data, verify=False)
    if res.status_code == 200:
        return res.json().get('data')
# print(get_sig())

def get_index():
    url = 'https://www.zhipin.com/job_detail/?query=%E7%88%AC%E8%99%AB&city=100010000&industry=&position='
    token = get_sig()
    headers = {
        "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36",
        "cookie": f"__zp_stoken__={token}"
    }
    res =requests.get(url,headers=headers)
    print(res.text)
    print(headers)
get_index()

```
