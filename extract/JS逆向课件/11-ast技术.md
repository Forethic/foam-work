

# `AST`技术专题

## 1、什么是`AST`技术

`AST`（Abstract Syntax Tree），译为抽象语法树，是编译原理中的一个概念，为源代码的抽象语法结构的树状表示，树上的每个节点都表示源代码中的一种结构，这种数据结构可以类别为一个大的` JSON `对象。通过 `AST` 技术，我们面对的就不再是各种符号混杂空格而成的文本字符串，而是一个严谨规范的树形结构，我们可以通过对 `AST `树节点的一系列操作，借助机器高效且精准地修改代码。

AST 的用途很广，IDE 的语法高亮、代码检查、格式化、压缩、转译等，都需要先将代码转化成 AST 再进行后续的操作，ES5 和 ES6 语法差异，为了向后兼容，在实际应用中需要进行语法的转换，也会用到 AST。AST 并不是为了逆向而生，但做逆向学会了 AST，在解混淆时可以如鱼得水。

        通过 AST 解析网站：https://astexplorer.net/，左侧为我们要输入的 JavaScript 代码，右侧为 AST 树状结构：
![image-20230221214832595](images\image-20230221214832595.png)

**AST对爬虫工程师有什么意义或者用途？**

随着技术的革新，越来越多的前端为了保护其网站不被第三方爬取，使用了大量的混淆代码，让爬虫工程师越来越难找到其核心参数加密代码。

有时候定位到加密的地方，面对一大坨乱如麻的代码，内心也是拒绝的。

这个时候，希望有一种工具，**尽可能的将被混淆的代码进行处理，使其可读性大大增加**，使爬虫工程师能够轻而易举的进行定位，抠出其核心的加密代码。

AST用在这里，只是一种辅助工具，并不能直接帮你找到核心的代码，还是得自己去分析，静态分析也好，动态调试也好，目的只有一个

### AST在编译中的位置

在编译原理中，编译器转换代码通常要经过三个步骤：词法分析（Lexical Analysis）、语法分析（Syntax Analysis）、代码生成（Code Generation），下图生动展示了这一过程：

![image-20230314160019655](images\image-20230314160019655.png)

## 2、解混淆`API`学习

参考地址：https://evilrecluse.top/Babel-traverse-api-doc/#/

在线解析：https://astexplorer.net/ 

```javascript
var a = "\u0068\u0065\u006c\u006c\u006f\u002c\u0041\u0053\u0054"
```

**1. type:  表示当前节点的类型，我们常用的类型判断方法 t.is \**\**\**(node),就是判断当前的节点是否为某个类型。**

**2. start: 表示当前节点的起始位。**

**3. end:  表示当前节点的末尾。**

**4. loc :  表示当前节点所在的行列位置，里面也有start与end节点，这里的start与上面的start是不同的，这里的start是表示节点所在起始的行列位。置，而end表示的是节点所在末尾的行列位置。**

**5. errors:是File节点所特有的属性，可以不用理会。**

**6. program:包含整个源代码，不包含注释节点。**

**7. comments:源代码中所有的注释会在这里显示。**

**掌握：**

1、鼠标跟随

2、`ast`各节点学习

#### 常见节点类型

![image-20230302194407837](images\image-20230302194407837.png)

### 2.1 安装babel库

根据官网介绍,它是一个JavaScript 编译器，主要用于将 `ECMAScript 2015+ `版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。

1. `@babel/core`：Babel 编译器本身，提供了 babel 的编译 API；
2. `@babel/parser`：将 JavaScript 代码解析成 AST 语法树；
3. `@babel/traverse`：遍历、修改 AST 语法树的各个节点；
4. `@babel/generator`：将 AST 还原成 JavaScript 代码；
5. `@babel/types`：判断、验证节点的类型、构建新 AST 节点等。

```javascript
npm install @babel/core --save-dev
```

**看一个小案例**

#### 2.1.1 parser库使用

将JavaScript源代码 转换成一棵`AST`树、返回结果(在这里赋值给`ast`)是**一个JSON结构的数据**。

```javascript
const parse = require('@babel/parser')

// JS 转 ast语法树
jscode = "\u0068\u0065\u006c\u006c\u006f\u002c\u0041\u0053\u0054";
let ast = parse.parse(jscode);
console.log(JSON.stringify(ast,null,'\t'))
```



#### 2.1.2 `traverse`库学习

节点插件编写与节点转化

##### 1、path属性语法学习

```javascript
var a = "\u0068\u0065\u006c\u006c\u006f\u002c\u0041\u0053\u0054";
```

`path.node`:表示当前path下的node节点

`path.toString()`:当前路径所对应的源代码

`path.parentPath`:用于获取当前path下的父path，多用于判断节点类型

`path.container`:用于获取当前path下的所有兄弟节点(包括自身)

`path.type`:获取当前节点类型

`path.get('')`:获取path的子路径

```javascript
v1 = {
    VariableDeclarator(path){
        // console.log(path.node); // 表示当前path下的node节点
         // console.log(path.type) // 获取当前节点类型
        // console.log(path.toString()); // 用来获取当前遍历path的js源代码
        // console.log(path.parentPath.node);  //用于获取当前path下的父path，多用于判断节点类型
        Declarator = path.parent   // 获取父节点
        // console.log(Declarator);
        // console.log(Declarator.get('declarations').toString()) //查找下一个节点
        // console.log(Declarator.get('declarations.0.init').toString()) // 查找节点值
        // console.log(path.get('init').toString());  // 获取下面的节点
        // var s =  path.parentPath.node == path.parent  //用于获取当前path下的父node
        // console.log(path.container);   // 用于获取当前path下的所有兄弟节点(包括自身)
    }
}
traverse(_ast,v1)
```



##### **2、path方法学习**

`path.replaceWith`:(单)节点替换函数

```javascript
path.replaceWith(newNode);
```

**2.1 简单拼接还原**

还原数字相加：`var b = 1 + 2`

还原字符串拼接：`var c = "coo" + "kie"`

还原在一行的：`var a = 1+1,b = 2+2;var c = 3;`

```javascript
const visitor1 =
{
    BinaryExpression(path){
        let {left,operator,right} = path.node;
        if (types.isNumericLiteral(left) && operator == "+" && types.isNumericLiteral(right)){
            value = left.value + right.value
            console.log(value);
            path.replaceWith(types.valueToNode(value))   // 进行方法替换
         }
        // 处理字符串拼接  var b = "he" + "llo"
        if (types.isStringLiteral(left) && types.isStringLiteral(right) && operator == '+'){
             console.log(path.toString())
             let value = left.value + right.value
             path.replaceWith(types.valueToNode(value))
        }
    }
}
```



`replaceWithMultiple `多节点替换函数，调用方式:

```javascript
path.replaceWithMultiple(ArrayNode);
```

实参一般是 Array 类型，它只能用于 Array 的替换。即所有需要替换的节点在一个Array里面

举例：对如下变量进行处理

```javascript
替换前：var arr =  '3,4,0,5,1,2'['split'](',')
替换后：var arr = ["3", "4", "0", "5", "1", "2"]
```

插件编写如下

```javascript
visitor4 = {
    CallExpression(path){
        let {callee,arguments} = path.node
        let object = callee.object.value;
        let property = callee.property.value;
        let argument = arguments[0].value;
         array = object[property](argument)
         path.replaceWithMultiple(types.valueToNode(array))
    }
}
```



**2.2、当`ast`遇到自执行方法还原**

```javascript
!(function (){
    console.log('123')
})
```

插件编写

```javascript
const visit = {
    UnaryExpression(path)
    {
        let {argument} = path.node;
        if (!types.isFunctionExpression(argument))
        {
            return;
        }
        let {body,id,params} = argument;
        if (id != null  || params.length != 0 )
        {
            return;
        }
        path.replaceWithMultiple(body.body)
    }
}
traverse(ast,visit)
```



**2.3、通用插件**

假设 `var d =true?1:2;` 还需要编写插件规则，有没有通用模板？

```javascript
const constantFold = {
    "BinaryExpression|UnaryExpression|ConditionalExpression"(path) {
        // 防止溢出
        if(path.isUnaryExpression({operator:"-"}) ||
    	   path.isUnaryExpression({operator:"void"}))
    	{
    		return;
    	}
        const {confident, value} = path.evaluate();
        if (!confident)
            return;
        if (typeof value == 'number' && (!Number.isFinite(value))) {
            return;
        }
        path.replaceWith(types.valueToNode(value));
    },
}
traverse(_ast, constantFold);
```

**2.4 evaluate()方法学习**

针对作用域和引用，直接依据引用来计算出执行结果。

例子：`navigator["\x75\x73"+"\x65\x72"+"\x41\x67"+"\x65\x6e"+"\x74"]`

例子2：

```javascript
function _xl(){
	x = 1 + 2 + 3 +4 + 5 + 6 + 7
}
```

插件编写

```javascript
// confident 属性表示置信度
visitor2 = {
    "BinaryExpression|Identifier"(path){
        const {confident,value} = path.evaluate();
        confident && path.replaceInline(types.valueToNode(value))
    }
}
traverse(_ast,visitor2)
```



**2.5 编码类型还原**

处理前:

```javascript
var a = 0x25,b = 0b10001001,c = 0o123456,
d = "\x68\x65\x6c\x6c\x6f\x2c\x41\x53\x54",
e = "\u0068\u0065\u006c\u006c\u006f\u002c\u0041\u0053\u0054";
```

处理后:

```javascript
var a = 37,b = 137,c = 42798,d = "hello,AST",e = "hello,AST";
```

插件编写:

```javascript
const transform_literal = {
  NumericLiteral({node}) {
    if (node.extra && /^0[obx]/i.test(node.extra.raw)) {
      node.extra = undefined;
    }
  },
  StringLiteral({node}) 
  {
    if (node.extra && /\\[ux]/gi.test(node.extra.raw)) {
      node.extra = undefined;
    }
  },
}
```



#### 2.1.3 fs库学习

读取文件

```javascript
xx = _fs.readFileSync('test.js',{encoding:'utf-8'})
```

写入文件

```javascript
fs.writeFile('decode.js', code, (err)=>{});
```



#### 2.1.4 `generator`库学习

生成新的`js code`，并保存到文件中输出

```javascript
const generator = require("@babel/generator").default;
let {code} = generator(ast);
_fs.writeFile('decode.js', code, (err)=>{});
```

**扩展**

1 删除所有注释

```javascript
const output = generator(ast,opts = {"comments":false},code);
```

2 Unicode转中文或者其他非ASCII码字符。

```javascript
const output = generator(ast,opts = {jsescOption:{"minimal":true}},code);
```



## 3、OB混淆高级

打开著名的JavaScript在线混淆网站:

```
https://obfuscator.io/
```



## 4、开源工具分享

地址：https://github.com/echo094/decode-js?tab=readme-ov-file

地址：https://github.com/j4k0xb/webcrack

地址：https://github.com/yufeng8/decodeObfuscator



## 5、作业练习

```javascript
const code = `
const a = !![]+!![]+!![];      // 还原后 a = 3
const b = Math.floor(12.34 * 2.12)
const c = 10 >> 3 << 1
const d = String(21.3 + 14 * 1.32)
const e = parseInt("1.893" + "45.9088")
const f = parseFloat("23.2334" + "21.89112")
const g = 20 < 18 ? '未成年' : '成年'
`
```

作业答案：

注：该方法会对 path 对象进行执行操作，自动计算出结果，返回一个对象，其中的 `confident` 属性表示置信度，`value` 表示计算结果，使用 `types.valueToNode()` 方法创建节点









