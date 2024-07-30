# 15 COOkIE 反爬虫

## 15.1 cookie 反爬简介

Cookie 反爬虫指的是服务器端通过校验请求头中的 Cookie 值来区分正常用户和爬虫程序的手段，这种手段被广泛应用在 Web 应用中。

### 15.1.1 cookie 加密原理

![image-20220824165530043](images\image-20220824165530043.png)

## 15.2 cookie 逆向实践 1

- 学习响应 cookie 和 session 的处理
- 学习基于首页返回的 cookie 值

### 15.2.1 逆向目标

- 地址：http://www.zjmazhang.gov.cn/hdjlpt/published?via=pc

- 接口：http://www.zjmazhang.gov.cn/hdjlpt/letter/pubList

- 对抗：`cookie`反爬虫处理

### 15.2.2 逆向分析

- 可以发现抓取接口数据，需要携带对应的 cookie 才能正常获取

  ![image-20220823170811179](images\image-20220823170811179.png)

### 15.2.3 逆向结果

- 使用 python 先提取 cookie，然后携带请求可以正常突破

```python
import re
import httpx
from utils import ua


def get_ck():
    url = 'http://www.zjmazhang.gov.cn/hdjlpt/published?via=pc'
    res = httpx.request('GET',url)
    cookie = res.cookies.get('szxx_session')
    token = re.findall(r"var _CSRF = '(.*?)';",res.text)[0]
    return cookie,token

def index():
    c, t = get_ck()
    url = 'http://www.zjmazhang.gov.cn/hdjlpt/letter/pubList'
    headers = {
        'user-agent': ua.FakeChromeUA.get_ua(),
        'X-CSRF-TOKEN': t,
        'Cookie': 'XSRF-TOKEN={};szxx_session={}'.format(t, c)
    }
    data = {
        "offset": "0",
        "limit": "20",
        "site_id": "759010",
    }
    res = httpx.post(url,data=data,headers=headers)
    if res.status_code == 200:
        print(res.text)
    else:
        print('请求错误')

index()
```

## 15.3. cookie 逆向实战

- 学习 JS 设置的 cookie 处理
- 学习轻度 JS 混淆处理

### 15.3.1 逆向目标

- 首页：https://sh.esfxiaoqu.zhuge.com/page2/
- 目标：cookie：`acw_sc__v2`

### 15.3.2 逆向分析

直接用代码带上参数请求这个接口，发现并没有给我们数据，而是给了一段 js。但是这个 js 可以明显的看到，set 了一个 cookie，`acw_sc__v2`，接下来就简单了，直接分析这段 js 就好了

![图片](https://mmbiz.qpic.cn/mmbiz_png/ZVQY25hppNGSUFcCx2M8fGKw4yYPR6nYI42U6wXejCZiaPqDvfvDXzpoS5R8gQcf7XWWbOKpO52cWic35J7VnWZw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 15.3.2.1 JS 调试分析

- 在浏览器里面给`arg2`下个断点观察对应参数

![image-20220824162743929](images\image-20220824162743929.png)

- 找出对应的核心代码，方便后续的调试

![image-20220824162627614](images\image-20220824162627614.png)

- 老规矩，还原对应的参数和逻辑

![image-20220824162919831](images\image-20220824162919831.png)

### 15.3.3 逆向结果

#### 15.3.3.1 Js 还原

#### 15.3.3.2 python 还原

```python
import re
import execjs
import requests

url = 'https://sh.esfxiaoqu.zhuge.com/page2/'
headers = {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
    "Accept-Encoding": "gzip, deflate, br",
    "Accept-Language": "zh-CN,zh;q=0.9",
    "Cache-Control": "no-cache",
    "Connection": "keep-alive",
    "Host": "sh.esfxiaoqu.zhuge.com",
    "Pragma": "no-cache",
    "sec-ch-ua": "\"Chromium\";v=\"104\", \" Not A;Brand\";v=\"99\", \"Google Chrome\";v=\"104\"",
    "sec-ch-ua-mobile": "?0",
    "sec-ch-ua-platform": "\"Windows\"",
    "Sec-Fetch-Dest": "document",
    "Sec-Fetch-Mode": "navigate",
    "Sec-Fetch-Site": "same-origin",
    "Sec-Fetch-User": "?1",
    "Upgrade-Insecure-Requests": "1",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36"
}

def get_complete_cookie():
    complete_cookie = {}
    # 第一次不带参数访问首页，获取 acw_tc 和 acw_sc__v2
    response = requests.get(url=url, headers=headers)
    complete_cookie.update(response.cookies.get_dict())
    arg1 = re.findall("arg1='(.*?)'", response.text)[0]
    with open('tiaoshi.js', 'r', encoding='utf-8') as f:
        acw_sc_v2_js = f.read()
    acw_sc__v2 = execjs.compile(acw_sc_v2_js).call('xl', arg1)
    complete_cookie.update({"acw_sc__v2": acw_sc__v2})
    # 第二次访问首页，获取其他 cookies
    response2 = requests.get(url=url, headers=headers, cookies=complete_cookie)
    complete_cookie.update(response2.cookies.get_dict())
    return complete_cookie

if __name__ == '__main__':
    complete_cookie = get_complete_cookie()
```

## 15.4 加速乐 cookie 处理

### 15.4.1 逆向目标

- 首页：https://www.mafengwo.cn/i/5376978.html
- 目标：cookie：`__jsl_clearance_s`

### 15.4.2 逆向过程

### 15.4.3 抓包分析

打开抓包软件（Fiddler）或者无痕浏览器，输入目标链接，会出现以下几个状态码；分别是 521、521、200，我们的目标数据就在状态码为 200 的里面：

![image-20230628143321364](C:\Users\XL\AppData\Roaming\Typora\typora-user-images\image-20230628143321364.png)

查看第一个状态码为 521 返回的数据，有服务器返回的 cookie 和一段类似表情包（AAEncode）的 js 代码：

![image-20230628143507984](C:\Users\XL\AppData\Roaming\Typora\typora-user-images\image-20230628143507984.png)

我们把这段 js 代码拿到浏览器控制台调试下，发现打印出来了 cookie:

![image-20230628143631906](C:\Users\XL\AppData\Roaming\Typora\typora-user-images\image-20230628143631906.png)

再把上面的 set-cookie 和 js 代码生成的 cookie 去和另外两个请求头对比，发现和第二个状态码为 521 的请求头带的 cookie 一模一样，而状态码为 200 的请求头带的 cookie 只有‘\_\_jsluid_s’是一样的，所以这里可以猜测下网站执行顺序：第一次请求服务器，服务器返回 cookie 和一段（AAEncode）js 代码，再用第一个请求返回的代码生成的 cookie 和服务器返回的 cookie 去请求服务器，服务器再一次返回一段混淆代码，最后再用混淆代码生成 cookie 和服务器第一次返回的 cookie 去请求目标网站，最终才返回我们要的数据。第二次响应数据是一段混淆代码，这段代码比较长拿到 source 面板进行调试：
![image-20230628143757477](C:\Users\XL\AppData\Roaming\Typora\typora-user-images\image-20230628143757477.png)

这里我们可以 hook 一下 cookie 设置的位置

```javascript
(function () {
  //严谨模式 检查所有错误
  "use strict";
  //document 为要hook的对象   这里是hook的cookie
  var cookieTemp = "";
  Object.defineProperty(document, "cookie", {
    //hook set方法也就是赋值的方法
    set: function (val) {
      //这样就可以快速给下面这个代码行下断点
      //从而快速定位设置cookie的代码
      debugger;
      console.log("Hook捕获到cookie设置->", val);
      cookieTemp = val;
      return val;
    },
    //hook get方法也就是取值的方法
    get: function () {
      return cookieTemp;
    },
  });
})();
```

后边就是一些常规调试了
