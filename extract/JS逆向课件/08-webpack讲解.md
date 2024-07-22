## `WebPack`打包

`webpack`是一个基于模块化的打包（构建）工具, 它把一切都视作模块

**概念：**

​       `webpack`是 `JavaScript` 应用程序的模块打包器,可以把开发中的所有资源（图片、js文件、css文件等）都看成模块，通过loader（加载器）和`plugins`（插件）对资源进行处理，打包成符合生产环境部署的前端资源。所有的资源都是通过`JavaScript`渲染出来的。



如果一个页面大部分是script标签构成，80%以上是`webpack`打包。

**地址**：http://cls.cn/telegraph

![img](https://app.yinxiang.com/FileSharing.action?hash=1/7d9dde72b8262d871a27f10af50a9b44-286201)



### 1. `webpack`打包简介

![img](https://app.yinxiang.com/FileSharing.action?hash=1/62be23ee03da0e043ff4b088eb0872cd-72813)



#### 1.0 多个`JS`文件打包：

​		如果模块比较多，就会将模块打包成JS文件, 然后定义一个全局变量` window["webpackJsonp"] = [ ]`，它的作用是存储需要动态导入的模块，然后重写 `window["webpackJsonp"] `数组的 push( ) 方法为` webpackJsonpCallback( )`,也就是说 `window["webpackJsonp"].push( )` 其实执行的是 `webpackJsonpCallback( )`,`window["webpackJsonp"].push( )`接收三个参数,第一个参数是模块的ID,第二个参数是 一个数组或者对象,里面定义大量的函数,第三个参数是要调用的函数(可选)

![img](https://app.yinxiang.com/FileSharing.action?hash=1/46d51af6b6946548adccc97f31a249ef-20029)



#### 1.1 `webpack`数组形式

+ 给需要处理业务的模块进行打包，通过下标取值。

```javascript
!function(e) {
    var t = {};

    // 加载器  所有的模块都是从这个函数加载 执行
    function n(r) {
        if (t[r])
            return t[r].exports;
        var o = t[r] = {
            i: r,
            l: !1,
            exports: {}
        };
        return e[r].call(o.exports, o, o.exports, n),
            o.l = !0,
            o.exports
    }

    n(0)
}
    ([
        function () {
            console.log('123456')
        },

              function () {
            console.log('模块2')
        },
    ])
```

#### 1.2 `webpack`对象形式

+ 给需要处理业务的模块进行打包，通过`key`取值。

```javascript
!function(e) {
    var t = {};
    //  所有的模块 都是从这个加载器 执行的  分发器
    function n(r) {
        if (t[r])
            return t[r].exports;
        var o = t[r] = {
            i: r,
            l: !1,
            exports: {}
        };
        return e[r].call(o.exports, o, o.exports, n),
        o.l = !0,
        o.exports
    }
   n('xialuo')  // 对象 根据KEY 找模块
}({

        0: function () {
            console.log('我是模块1  负责加密')
        },

        'xialuo': function () {
            console.log('我是模块2  负责解密')
        },

        2: function () {
            console.log('我是模块3  负责爬数据')
        }
    }
);
```



### 2. `webpack`教学

示范：QQ音乐，需要补环境来着

#### 2.1 逆向目标

+ 首页：https://36kr.com/

+ 逆向参数：`password: 8cbf7f88e70300def68533a74c77b785e11d743c77627b624`



### 3. `webpack`教学

#### 3.1 逆向目标

+ 地址：https://ec.minmetals.com.cn/open/home/purchase-info/?tabIndex=1
+ 接口： "aHR0cHM6Ly9lYy5taW5tZXRhbHMuY29tLmNuL29wZW4vaG9tZXBhZ2UvemJzL2J5LWx4LXBhZ2U="
+ 目标：响应数据`ZmQzOTI0MjBlNnic7VvJcuM4Ev0aHqXADuRRtKWIPnT0uU8KEABtdWkbLVXj`

#### 3.2 逆向分析

打开目标网站，往下拉点击翻页，在开发者工具可以抓到这个包，其中接口请求到的表单数据是加密之后的密文

![image-20230307204143641](images\image-20230307204143641.png)



##### 3.2.1 关键字搜索 

这个时候可以尝试再在该文件里搜索params、发现很难断住！然后上面的`homepage/public`是返回密钥，看下能否从这里入手进行搜索，就比较可疑，下个断点

![image-20230307204848425](images\image-20230307204848425.png)

**数据处理**

往上找可以发现 `t = new v["a"], v = t("9816");` 所以要处理的话就是扣下9816这个包即可，然后对加载器进行处理。

具体代码就不贴了、老铁们可以看视频教程哈

![image-20230307211610908](images\image-20230307211610908.png)



### 4. 开源插件使用

资料见课件提供

工具：https://gitcode.net/zjq592767809/webpack_ast

#### 4.1 使用命令行的方式

```
node webpack_mixer.js -l runtime.62249a5.js -m app.597640f.js -o webout.js
```

参数说明：

```
-l 加载器的js路径
加载器的js特征：
    1.以自执行函数开头
    2.定义导出函数，类似 return e[n].call(r.exports, r, r.exports, d), r.l = !0, r.exports
    3.为导出函数添加多个方法，类似d.e，d.m，d.n等等
-m 函数模块的js路径
	函数模块的js特征：
	1.一般以(window.webpackJsonp开头
-o 输入结果的js路径
```

#### 4.2 教学案例

地址：https://y.qq.com/n/ryqq/toplist/4

需求：解析有道响应的数据

##### 4.2.1 接口分析

直接搜索参数进行定位、可以发现数据解密的位置



复制整个`JS`文件使用半自动导出即可

```javascript
node webpack_mixer.txt -l runtime.62249a5.js -o webout.js
# 如果有多个JS文件怎么操作
node webpack_mixer.txt -l 加载器.js -m 模块1.js -m 模块2.js  -o 输出.js
```

```
安装模块 npm install @babel/core --save
```



### 总结

+ 找到这个加载器
+ 找到调用模块
+ 构造一个自执行方法
+ 导出加密方法
+ 编写自定义方法 按照流程加密







