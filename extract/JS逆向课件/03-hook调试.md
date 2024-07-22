### 3.5  hook技术

Hook 是一种钩子技术，在系统没有调用函数之前，钩子程序就先得到控制权，这时钩子函数既可以加工处理（改变）该函数的执行行为，也可以强制结束消息的传递。简单来说，**修改原有的 JS 代码就是 Hook**。

**Hook 技术之所以能够实现有两个条件：**

- 客户端拥有` JS `的最高解释权，可以决定在任何时候注入` JS`，而服务器无法阻止或干预。服务端只能通过检测和混淆的手段，另 Hook 难度加大，但是无法直接阻止。
- 除了上面的必要条件之外，还有一个条件。就是 JS 是一种弱类型语言，同一个变量可以多次定义、根据需要进行不同的赋值，而这种情况如果在其他强类型语言中则可能会报错，导致代码无法执行。js 的这种特性，为我们 Hook 代码提供了便利。



**注意：**`JS` 变量是有作用域的，只有当被 `hook` 函数和 `debugger `断点在同一个作用域的时候，才能 hook 成功。

##### 3.5.1 Hook步骤：

+ 1 寻找hook的点
+ 2 编写hook逻辑
+ 3 调试



**注**：最常用的是`hook cookie response open 表单`

#####  3.5.2 hook 数据方法

站点："aHR0cHM6Ly9mYW55aS55b3VkYW8uY29tL2luZGV4Lmh0bWwjLw=="

练习

```
aHR0cHM6Ly9lYy5taW5tZXRhbHMuY29tLmNuL2xvZ29uQWN0aW9uLmRv
aHR0cHM6Ly9qenNjLm1vaHVyZC5nb3YuY24vc2luY2UvZmFrZQ==
aHR0cHM6Ly93d3cuYmlyZHJlcG9ydC5jbi9ob21lL2FjdGl2aXR5L3BhZ2UuaHRtbA==
```

+  `JavaScript` 中 `JSON.stringify()` 方法用于将` JavaScript` 对象或值转换为 `JSON` 字符串，

+ `JSON.parse()` 方法用于将一个 `JSON `字符串转换为`JavaScript` 对象

```javascript
(function() {
    var _stringify = JSON.stringify;
    JSON.stringify = function(ps) {
        console.log("Hook JSON.stringify ——> ", ps);
        return _stringify(ps);  // 不改变原有的执行逻辑 
    }
})();


(function() {
    var _parse = JSON.parse;
    JSON.parse = function(ps) {
        console.log("Hook JSON.parse ——> ", ps);
        return _parse(ps);  // 不改变原有的执行逻辑 
    }
})();

```

​		首先定义了一个变量 `stringify` 保留原始 `JSON.stringify` 方法，然后重写 `JSON.stringify` 方法，遇到 `JSON.stringify` 方法就会执行 `debugger` 语句，会立即断下，最后将接收到的参数返回给原始的 `JSON.stringify` 方法进行处理，确保数据正常传输



##### 3.5.3 hook XHR请求

**案例地址：**https://www.qimai.cn

​		定义了一个变量 `open` 保留原始 `XMLHttpRequest.open` 方法，然后重写 `XMLHttpRequest.open` 方法，判断如果 rnd 字符串值在 URL 里首次出现的位置不为 -1，即 URL 里包含 `analysis`字符串，则执行 `debugger` 语句，会立即断下。

```javascript
(function () {
    var open = window.XMLHttpRequest.prototype.open;
    window.XMLHttpRequest.prototype.open = function (method, url, async) {
        if (url.indexOf("analysis") != -1) {
            debugger;
        }
        return open.apply(this, arguments);
    };
})();
```

##### 3.5.4 hook cookie

`WEBAPI`地址：https://developer.mozilla.org/zh-CN/docs/Web/API

`Object.defineProperty `为对象的属性赋值，替换对象属性

基本语法：`Object.defineProperty(obj, prop, descriptor)`，它的作用就是直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，接收的三个参数含义如下：

+ `obj`：需要定义属性的当前对象；

+ `prop`：当前需要定义的属性名；

```javascript
Object.defineProperty(user,"age",{
     get:function(){
          console.log("这个人来获取值了！！");
          return count;
     },
     set:function(newVal){
          console.log("这个人来设置值了！！");
          count=newVal+1;
     }
})
```

##### 3.5.5 hook constructor 

```javascript
(function () {
    let constructorCache = Function.prototype.constructor;
    Function.prototype.constructor = function (string) {
        if (string === "debugger") {
            console.log("Hook constructor debugger!");
            return function () {};
        }
        return constructorCache(string);
    };
})();

```



**cookie 示范**

示范例子：http://q.10jqka.com.cn/

cookie 钩子用于定位 cookie 中关键参数生成位置，以下代码演示了当 cookie 中匹配到了 `v`， 则插入断点：

```JavaScript
(function () {
  var cookieTemp = '';
  Object.defineProperty(document, 'cookie', {
    set: function (val) {
      if (val.indexOf('v') != -1) {
        	debugger;
      }
      console.log('Hook捕获到cookie设置->', val);
      cookieTemp = val;
      return val;
    },
    get: function () {
      return cookieTemp;
    },
  });
})();
```

注：正常`hook cookie`操作的时候需要清除下`cookie`

##### 3.5.5 hook插件开发

**油猴工具介绍**

[Tampermonkey](https://links.jianshu.com/go?to=https%3A%2F%2Ftampermonkey.net%2F)是一款免费的浏览器扩展和最为流行的用户脚本管理器，它适用于 Chrome, Microsoft Edge, Safari, Opera Next, 和 Firefox。
 以上是油猴官网给出的介绍。它可以让用户自行在添加脚本，并在开启对应页面时应用。如果你了解"**脚本注入**"，你可以把它认为是一个给自己注入脚本的一个工具。

**用户油猴脚本**：https://greasyfork.org/zh-CN/scripts

...... 大家可以自行娱乐娱乐， 比如说啊VIP电影免费破解

| 选项         | 含义                                                         |
| :----------- | :----------------------------------------------------------- |
| @name        | 脚本的名称                                                   |
| @namespace   | 命名空间，用来区分相同名称的脚本，一般写作者名字或者网址就可以 |
| @version     | 脚本版本，油猴脚本的更新会读取这个版本号                     |
| @description | 描述这个脚本是干什么用的                                     |
| @author      | 编写这个脚本的作者的名字                                     |
| **@match**   | 从字符串的起始位置匹配正则表达式，只有匹配的网址才会执行对应的脚本，例如 `*` 匹配所有，`https://www.baidu.com/*` 匹配百度等，可以参考 Python re 模块里面的 `re.match()` 方法，允许多个实例 |
| **@include** | 和 @match 类似，只有匹配的网址才会执行对应的脚本，但是 @include 不会从字符串起始位置匹配，例如 `*://*baidu.com/*` 匹配百度，具体区别可以参考 **TamperMonkey 官方文档**[8] |
| @icon        | 脚本的 icon 图标                                             |
| @grant       | 指定脚本运行所需权限，如果脚本拥有相应的权限，就可以调用油猴扩展提供的 API 与浏览器进行交互。如果设置为 none 的话，则不使用沙箱环境，脚本会直接运行在网页的环境中，这时候无法使用大部分油猴扩展的 API。如果不指定的话，油猴会默认添加几个最常用的 API |
| @require     | 如果脚本依赖其他 JS 库的话，可以使用 require 指令导入，在运行脚本之前先加载其它库 |
| **@run-at**  | 脚本注入时机，该选项是能不能 hook 到的关键，有五个值可选：`document-start`：网页开始时；`document-body`：body出现时；`document-end`：载入时或者之后执行；`document-idle`：载入完成后执行，默认选项；`context-menu`：在浏览器上下文菜单中单击该脚本时，一般将其设置为 `document-start` |

开发一个hook脚本

```javascript
// ==UserScript==
// @name         XL-调试逆向1.0
// @namespace    http://tampermonkey.net/
// @version      2024-05-29
// @description  分析逆向数据日志使用
// @author       You
// @match        *://*/*
// @run-at      document-start
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // Your code here...
    var _stringify = JSON.stringify;
    JSON.stringify = function(ps) {
        console.log("Hook JSON.stringify ——> ", ps);
        return _stringify(ps);

            var _parse = JSON.parse;
    JSON.parse = function(ps) {
        console.log("Hook JSON.parse ——> ", ps);
        return _parse(ps);
    }
    }
})();
```



### 3.6  JavaScript请求技术

#### 3.6.1  XMLHttpRequest

****

地址：https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest

`XMLHttpRequest`（XHR）对象用于与服务器交互。通过 XMLHttpRequest 可以在不刷新页面的情况下请求特定 URL，获取数据。这允许网页在不影响用户操作的情况下，更新页面的局部内容。`XMLHttpRequest` 在 [AJAX](https://developer.mozilla.org/zh-CN/docs/Glossary/AJAX) 编程中被大量使用。

[`XMLHttpRequest.open()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/open)

方法初始化一个新创建的请求，或重新初始化一个请求。

```
xhrReq.open(method, url, async);
```

[`XMLHttpRequest.send()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/send)

发送请求。如果请求是异步的（默认），那么该方法将在请求发送后立即返回。

方法接受一个可选的参数，其作为请求主体；如果请求方法是 GET 或者 HEAD,则应将请求主体设置为 null。

```
xhrReq.send(body)
```

[`XMLHttpRequest.setRequestHeader()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/setRequestHeader)

设置 HTTP 请求头的值。必须在 `open()` 之后、`send()` 之前调用 `setRequestHeader()` 方法。

```javascript
myReq.setRequestHeader(header, value); 
```

[`XMLHttpRequest.onreadystatechange`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/readystatechange_event)

当 `readyState` 属性发生变化时，调用的事件处理器。

测试地址：http://www.cninfo.com.cn/new/commonUrl?url=disclosure/list/notice#szseGem

```javascript
// 请求参数
body = 'column=szse_gem_latest&pageNum=2&pageSize=30&sortName=&sortType=&clusterFlag=true'

// 构造请求
let e = {
    'url':"http://www.cninfo.com.cn/new/disclosure",
    'method':'POST',
    "data": body
}
var h  = new  XMLHttpRequest(); 
// 初始化链接
h.open(e.method,e.url,true);
// 设置头部
h.setRequestHeader('accept','application/json;charset=UTF-8');
// 接收响应
    h.onreadystatechange = function (){
        if (h.status===200){
            console.log(JSON.parse(h.response))
        }
    }
// 发请求  
h.send(e.data)
```



#### 3.6.2 axios

`*Axios*` 是一个基于` promise` 的网络请求库,可以用于浏览器和 `node.js Axios` 使用简单



官方地址：http://www.axios-js.com/

##### 1 get请求

```javascript
params = {
    id: 1,
    page: 2
}
headers = {
    "user-agent": '12321321'
}
axios.get('https://wzzdg.sun0769.com/political/index/politicsNewest',
    {
    	// 配置项
        params: params,
        headers: headers
    }).then(function (response) {
      // 接收回数据调
})
    .catch(function (error) {
        // 异常输出
    });
```

##### 2 post请求

```javascript
data = {
    "key":"value"
}
headers = {
  	"key":"value"
}
axios.post('you url',
    data, {
        headers: headers,
    }
).then(function (response) {
    // 接收回数据调
})
    .catch(function (error) {
       // 异常输出
    });
```

##### 3 Interceptors

+ 请求拦截器：在发送请求之前，可以借助一些函数来对请求的内容和参数做一些检测。若有问题可以直接取消请求。
+ 响应拦截器：当服务器返回响应数据时，响应拦截器会在我们拿到结果前预先处理响应数据。例如对响应数据做一些格式化处理，或者当响应失败时，可以做一些失败提醒和纪录。

```javascript
// npm install axios
axios = require('axios')
//设置请求拦截器
axios.interceptors.request.use(function (config) {
    console.log('请求拦截器 成功')
    config.headers['sign'] = 'lili'
    return config;
}, function (error) {
    console.log('请求拦截器 失败')
    return Promise.reject(error);
});

//设置响应拦截器
axios.interceptors.response.use(function (response) {
    console.log('响应拦截器 成功')
    console.log('调解密函数进行解密数据')
    //return response;
    return response.data; //修改响应数据
}, function (error) {
    console.log('响应拦截器 失败')
    return Promise.reject(error);
});

//发送请求
axios.get('http://httpbin.org/get').then(res=>console.log(res))
```

#### 3.6.3 异步请求

[node](https://so.csdn.net/so/search?q=node&spm=1001.2101.3001.7020) 中没有实现 fetch，你可以使用 [node-fetch](https://github.com/bitinn/node-fetch)，使得在 node 中也可以使用 fetch

##### 1 get请求

```javascript
const url = 'https://wzzdg.sun0769.com/political/index/politicsNewest'
async function get_test(url){
    const headers = {
        "User-Agent": "12321312",
    };
    const params = {
            id: 1,
            page: 2
        }
    const res = await fetch(url, {
        method: "GET",
        params:params,
        headers,
        credentials: "include"
})
    const data = await res.text();
    console.log(data)

}
// get_test(url)
```

##### 2 post请求

测试地址：https://www.landchina.com/#/plotPromotion

```javascript
async function postJSON(data) {
  try {
    const response = await fetch("https://api.landchina.com/landRecommend/recommend/list", {
      method: "POST", // or 'PUT'
      headers: {
        "Content-Type": "application/json",
        "referer": "https://www.landchina.com/",
      },
      body: JSON.stringify(data),
    });
    const result = await response.json();
    console.log("Success:", result);
  } catch (error) {
    console.error("Error:", error);
  }
}
const data = {"key","value"}
```






