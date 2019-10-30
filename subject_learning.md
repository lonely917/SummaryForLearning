# html_learning
1. HTML 是用来描述网页的一种语言;
2. HTML 指的是超文本标记语言 (Hyper Text Markup Language);
3. HTML 不是一种编程语言，而是一种标记语言 (markup language);
4. 标记语言是一套标记标签 (markup tag);
5. HTML 使用标记标签来描述网页;
6. HTML 标签对大小写不敏感,万维网联盟（W3C）在 HTML 4 中推荐使用小写，而在未来 (X)HTML 版本中强制使用小写。
7. 属性值应该始终被包括在引号内。双引号是最常用的，不过使用单引号也没有问题。
在某些个别的情况下，比如属性值本身就含有双引号，那么您必须使用单引号，例如：
name='Bill "HelloWorld" Gates' 
8. HTML 代码中的所有连续的空行（换行）也被显示为一个空格。
9. pre标签 预格式文本，保留空格和换行，很适合显示计算机代码。
10. hr标签来分隔文章中的小节是一个办法（但并不是唯一的办法）
11. 
```html
HTML 链接是通过 <a> 标签进行定义的。
<a href="http://www.w3school.com.cn">This is a link</a>

HTML 图像是通过 <img> 标签进行定义的。
<img src="w3school.jpg" width="104" height="142" />

<hr /> 标签在 HTML 页面中创建水平线。使用水平线 

注释<!-- This is a comment -->

<br/>换行
<p>段落前后自带空行
<sub>	定义下标字。
<sup>	定义上标字。
<ins>	定义插入字。
<del>	定义删除字。
<abbr>	定义缩写。
<acronym>定义首字母缩写。

html5中提供了<header>, <nav>, <section>, 以及 <footer> 来创建多列布局
```
样式是 HTML 4 引入的，它是一种新的首选的改变 HTML 元素样式的方式。通过 HTML 样式，能够通过使用 style 属性直接将样式添加到 HTML 元素，或者间接地在独立的样式表中（CSS 文件）进行定义。通过使用 HTML4.0，所有的格式化代码均可移出 HTML 文档，然后移入一个独立的样式表。style 属性淘汰了“旧的” bgcolor 属性，使用新的background-color。

```html
<html>
<body>
<h1 style="font-family:verdana">A heading</h1>
<p style="font-family:arial;color:red;font-size:20px;">A paragraph.</p>
<h2 style="background-color:red">This is a heading</h2>
<p style="background-color:green">This is a paragraph.</p>
</body>
</html>
```

单独的样式表
```html
<html>

<head>
<style type="text/css">
h1 {color: red}
p {background-color:green;color: blue; font-size:20px;;}
</style>
</head>

<body>
<h1>header 1</h1>
<p>A paragraph.</p>
</body>

</html>
```

可以引用外部的css，也可以文件单独定义style，也可根据需要在标签里定义使用特殊的style。
```html
<link rel="stylesheet" type="text/css" href="mystyle.css">
```
超链接(a)的link和name属性，前者链接到某一目标，后者可以定义一个锚，页内标签。
(页内标签，可以用于页内指向或者页间跳转)。使用 Target 属性，你可以定义被链接的文档在何处显示，如果把链接的 target 属性设置为 "_blank"，该链接会在新窗口中打开。

```html
<a href="http://www.w3school.com.cn/" target="_blank">Visit W3School!</a>

<a name="tips">基本的注意事项 - 有用的提示</a>
<a href="#tips">有用的提示</a>
<a href="http://www.w3school.com.cn/html/html_links.asp#tips">有用的提示</a>
```
上述name属性进行标记的效果也可以通过id标记来实现，id name同名优先使用id名称。

请始终将正斜杠添加到子文件夹。
假如这样书写链接：href="http://www.w3school.com.cn/html"，
就会向服务器产生两次 HTTP 请求。
这是因为服务器会添加正斜杠到这个地址，然后创建一个新的请求，
就像这样：href="http://www.w3school.com.cn/html/"

```html
<img src="boat.gif" alt="Big Boat">
```
1. alt 属性用来为图像定义一串预备的可替换的文本。替换文本属性的值是用户定义的。
2. img标签可以使用map映射，map里面设置各个区域area，对指定区域分别进行超链接。
3. div - 布局块
4. span - 作用于一行

“块级元素”译为 block level element，“内联元素”译为 inline element。
块级元素在浏览器显示时，通常会以新行来开始（和结束）。


5. 使用不同的css样式，div使用class进行引用。比如将多种样式定义在head里(style中，其实放在body里面也不影响，style里面可以有多个类型)，然后再div或者span中使用class中引用。
6. div的class对应.开头的样式，div的id对应#开头的样式


## RWD

1. RWD 指的是响应式 Web 设计（Responsive Web Design）
2. RWD 能够以可变尺寸传递网页
3. RWD 对于平板和移动设备是必需的
4. Bootstrap 是最流行的开发响应式 web 的 HTML, CSS, 和 JS 框架。

HTML框架Frameset标签，嵌套多个frame，frame对应不同html文件，rows指定行比例，水平框架，cols指定列比例，垂直框架。noresize = "noresize"指定框架集中所有框架或者某一个框架不能拉伸。frameset的嵌套可以构造组合框架，水平和垂直的组合。利用frameset构造导航框架，左侧是列表，右侧是内容，左侧的url跳转通过target属性设置对应目标，目标名为右侧的frame对应的name属性内容，target控制跳转目标。除了制定对应html网页，还可以通过标签设置定位到固定的节，即a的name或者id属性定义的书签。

iframe内联框架，网页内显示网页，`和frame有什么不同?`宽高设置，边框设置，name属性用于target指定目标跳转。

frameset 和 frame一起配合使用，文档不含body标签，只是划分结构。iframe是在文档中嵌入一个子文档，可以在body中使用iframe。

```html
<body> 标签中的背景颜色（bgcolor）、背景（background）和文本（text）属性在最新的 HTML 标准（HTML4 和 XHTML）中已被废弃。W3C 在他们的推荐标准中已删除这些属性。
应该使用层叠样式表（CSS）来定义 HTML 元素的布局和显示属性。
对于颜色属性值可以是十六进制数、RGB 值或颜色名。

<body bgcolor="#000000">
<body bgcolor="rgb(0,0,0)">
<body bgcolor="black">
```
android布局文件xml的设计，类似通用。
background添加背景图片，默认平铺叠加。

```html
JavaScript 最常用于图片操作、表单验证以及内容动态更新。
<noscript> 标签提供无法使用脚本时的替代内容，比方在浏览器禁用脚本时，或浏览器不支持客户端脚本时。
```

html头部元素:
head，title，base，link，meta，script，style。
```html
<meta http-equiv="Refresh" content="10;url=http://www.w3school.com.cn" />
10s后自动刷新，利用于网址变更，重定向。
```
一些搜索引擎会利用meta元素的属性name和content去索引页面。
content 属性提供了名称/值对中的值。该值可以是任何有效的字符串。
content 属性始终要和 name 属性或 http-equiv 属性一起使用。

```html
<meta http-equiv="charset" content="iso-8859-1">
<meta name="keywords" content="HTML,ASP,PHP,SQL">
```

一些转义字符

符号| 含义| 十进制| 转义字符
-|-|-|-
--|不间断空格（non-breaking space）|`&nbsp;`| `&#160;`
<|less-than|`&lt;`|`&#60;`
=|equals-to|--|`&#61;`
>|greater-than|&gt;|`&#62;`

html实体符号参考手册
http://www.w3school.com.cn/tags/html_ref_entities.html

URL - Uniform Resource Locator
```
scheme://host.domain:port/path/filename

scheme - 定义因特网服务的类型。最常见的类型是 http
host - 定义域主机（http 的默认主机是 www）
domain - 定义因特网域名，比如 w3school.com.cn
:port - 定义主机上的端口号（http 的默认端口号是 80）
path - 定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）。
filename - 定义文档/资源的名称

http	超文本传输协议	以 http:// 开头的普通网页。不加密。
https	安全超文本传输协议	安全网页。加密所有信息交换。
ftp	文件传输协议	用于将文件下载或上传至网站。
file	 	您计算机上的文件。
```

十六进制格式用于在浏览器和插件中显示非标准的字母和字符。除了字母和字符以外，使用%加两位十六进制来表示，使用+标识空格。


ISP 的 web 服务器通常强大到能够由若干网站分享资源。您还要看一下 ISP 是否提供高效的负载平衡，以及必要的备份服务器。

```
HTML5	2012
XHTML5	2013
<!DOCTYPE html>标识文档类型为HTML5
<!DOCTYPE> 不是 HTML 标签。它为浏览器提供一项信息（声明），即 HTML 是用什么版本编写的。
```

XML 是一种必须正确标记且格式良好的标记语言。
XHTML 是以 XML 格式编写的 HTML。一种更为严格的web标准，要取代html。

### 如何从 HTML 转换到 XHTML
1. 向每张页面的第一行添加 XHTML <!DOCTYPE>
2. 向每张页面的 html 元素添加 xmlns 属性
3. 把所有元素名改为小写
4. 关闭所有空元素
5. 把所有属性名改为小写
6. 为所有属性值加引号

### 表单
表单form，不同的type，get和post会提交表单数据，必须是有name标识的，否则对应value不会被提交。fieldset表单分块，组合表单中各种元素。

form中的属性
```
accept-charset	规定在被提交表单中使用的字符集（默认：页面字符集）。
action	规定向何处提交表单的地址（URL）（提交页面）。
autocomplete	规定浏览器应该自动完成表单（默认：开启）。
enctype	规定被提交数据的编码（默认：url-encoded）。
method	规定在提交表单时所用的 HTTP 方法（默认：GET）。
name	规定识别表单的名称（对于 DOM 使用：document.forms.name）。
novalidate	规定浏览器不验证表单。
target	规定 action 属性中地址的目标（默认：_self）。
```
表单元素

```
input
select
textarea
```

对于button -> onclick响应

input元素的type属性，input可以是文本输入，多个单选，提交按钮。
```
类型	描述
text	定义常规文本输入。
radio	定义单选按钮输入（选择多个选择之一）
checkbox 复选框，多个或者零个
password 密码

submit	定义提交按钮（提交表单）

select 下拉选择则

textarea 文本域，多行文字输入。

button 可点击按钮。可以不在表单里。
```

### 关于button
```xhtml
<button type="button" onclick="alert('Hello World!')">Click Me!</button>
<br/>
<input type="button" onclick="alert('Hello World!')" value="Click Me!">
```

### HTML5 表单元素
HTML5 增加了如下表单元素：

```html
<datalist>
<keygen>
<output>
```html

HTML5 增加了多个新的输入类型：

```html
color
date
datetime
datetime-local
email
month
number
range
search
tel
time
url
week
```

H5对表单属性提供了更为多样的控制

HTML5 中默认的字符编码是 UTF-8。

block层级显示，可以自定义标签。

html列表ol，lu，每一个元素li。

### html图像
```html
<canvas>	定义使用 JavaScript 的图像绘制。
<svg>	定义使用 SVG 的图像绘制。
```

html5样式指南和代码约定
http://www.w3school.com.cn/html/html5_syntax.asp

html基本示例
http://www.w3school.com.cn/example/html_examples.asp


# javascript Learning

## 输出数据的四种方式
1. 使用 window.alert() 弹出警告框。
2. 使用 document.write() 方法将内容写到 HTML 文档中。
3. 使用 innerHTML 写入到 HTML 元素。
4. 使用 console.log() 写入到浏览器的控制台。

## script的位置
head，body或者外部文件，外部文件不能含有script标签，直接就是代码体。

## 一种脚本语言。何为脚本语言?
 
脚本语言又被称为扩建的语言，或者动态语言，是一种编程语言，用来控制软件应用程序，脚本通常以文本（如ASCII)保存，只在被调用时进行解释或编译。

## 字面量
数字、字符串、表达式、数组(使用中括号和逗号)、对象Object(json - js object notation)、函数字面量(??函数还字面量，类似定义函数)。

字面量（literal）是用于表达源代码中一个固定值的表示法（notation）。
{firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"}
变量var，var是关键字之一，js有很多关键字(依然有int double...)

JavaScript 对大小写是敏感的。JavaScript 使用 Unicode 字符集。Unicode 覆盖了所有的字符，包含标点等字符。JavaScript 会忽略多余的空格。您可以向脚本添加空格，来提高其可读性。您可以在文本字符串中使用反斜杠对代码行进行换行.注释类似java，c。

## 关于unicode和utf-8
后者是前者的一种实现形式  
http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html

## 双引号和单引号
1. 当您向变量分配文本值时，应该用双引号或单引号包围这个值。
2. 当您向变量赋的值是数值时，不要使用引号。如果您用引号包围数值，该值会被作为文本来处理。

## 如果重新声明 JavaScript 变量，该变量的值不会丢失。

## ??js的转义以及格式化输出??

## 数组
```js
var cars=new Array();
cars[0]="Saab";
cars[1]="Volvo";
cars[2]="BMW";
condensed array ：var cars=new Array("Saab","Volvo","BMW");
literal array：var cars=["Saab","Volvo","BMW"];
```
## Undefined
1. Undefined 这个值表示变量不含有值。
2. 可以通过将变量的值设置为 null 来清空变量。

```js
	typeof undefined//undefined
	typeof null//null
	(null === undefined)//false
	(null == undefined)//true
```

## ??var一个变量与new一个对象??

## JavaScript 对象是键值对的容器

## js方法，没有重载。

1. 您可以在不同的函数中使用名称相同的局部变量，因为只有声明过该变量的函数才能识别出该变量。
2. 只要函数运行完毕，本地变量就会被删除。
3. 在函数外声明的变量是全局变量，网页上的所有脚本和函数都能访问它。
4. JavaScript 变量的生命期从它们被声明的时间开始。
5. 局部变量会在函数运行以后被删除。
6. 全局变量会在页面关闭后被删除。
7. 如果变量在函数内没有声明（没有使用 var 关键字），该变量为全局变量。
8. 在 HTML 中, 全局变量是 window 对象: 所有数据变量都属于 window 对象。

## html事件
html事件：发生事件触发js行为,事件类型参考htmldom事件。
1. HTML 页面完成加载onload
2. HTML input 字段改变时
3. HTML 按钮被点击 onclick

## js中的字符串操作
1. ==数值相等 
2. ===数值和类型都相同

## 对于js对象和属性的概念?

```js
var x = "John";              // x是一个字符串
var y = new String("John");  // y是一个对象
	x.tmp = 21;//字符串没有属性，这样无效
	y.tmp = "ddd";//对象可以有属性
typeof(x) //string
typeof(y) //object
x == y//true 
x===y//false
x.length//4,原始值可以使用 JavaScript 的属性和方法
```

## html多个空格
 ```js
 document.write("&nbsp;&nbsp;"+"1"+"&nbsp;&nbsp;&nbsp;&nbsp;"+"23");
 document.write("<span style='white-space:pre;'>"+"  1        2    3    "+"</span>");
```
## js数值类型
?js数值类型转换，字符串和数值，以及var和int，有int类型么?没有int，有Number

## continue&break
1. continue 语句（带有或不带标签引用）只能用在循环中。
2. break 语句（不带标签引用），只能用在循环或 switch 中。
3. 通过标签引用，break 语句可用于跳出任何 JavaScript 代码块：
4. 类似java中带有标签的continue和break，不带标签的看以看作标签在最近的一个循环体开始处，当然带有标签的break可以不在循环中使用，标签指示代码体。

```js
1.2.toFixed(3); //1.200 ,小数点后3位
String的带参构造函数和其它类型的toString方法可以实现其它类型向String的转换
Number的带有string的构造函数和pasreXX方法可以实现字符串向number的转换

/runoob/i  是一个正则表达式。
runoob  是一个正则表达式主体 (用于检索)。
i  是一个修饰符 (表示搜索不区分大小写)。
```

## JavaScript RegExp 参考手册

## js的try，catch，以及抛出异常
## 浏览器调试js代码
## debugger 关键字用于停止执行 JavaScript，并调用调试函数。

## hoisting 变量提升
JavaScript 只有声明的变量会提升，初始化的不会。

```js
第一个示例相当于第二个示例
var x = 5; // 初始化 x

elem = document.getElementById("demo"); // 查找元素 
elem.innerHTML = x + " " + y;           // 显示 x 和 y y是undefined

var y = 7; // 初始化 y

第二个示例
var x = 5; // 初始化 x
var y;     // 声明 y

elem = document.getElementById("demo"); // 查找元素
elem.innerHTML = x + " " + y;           // 显示 x 和 y

y = 7;    // 设置 y 为 7
```
窃以为变量提升只是一种规则，语言规则，这是由解释器处理逻辑和语言设计者喜好观念决定。
另有严格模式strict mode，不允许js中使用未声明变量。

严格模式通过在脚本或函数的头部添加 "use strict"; 表达式来声明。ECMAScript5中新增。
严格模式要求更明确，避免混淆，提出一些新的保留字以备扩展使用。
```js
var x = 10;
var y = "10";
if (x == y)  //返回true，==的比较中，数据类型被忽略。
```
1. switch 语句会使用恒等计算符(===)进行比较，===比较数据类型和数值

2. java中==对于string 比较地址(引用的值)，equals比较string内容。
自定义类，一般要注意equals和hash方法的重写。首先判断是相同类型，然后比较字段内容等。

## js变量精度
所有的编程语言，包括 JavaScript，对浮点型数据的精确度都很难确定，js float 64位浮点型数据。
```js
var x = 0.1;
var y = 0.2;
var z = x + y            // z 的结果为 0.3
if (z == 0.3)            // 返回 false

但是下面这个例子！！
var z = (x * 10 + y * 10) / 10;       // z 的结果为 0.3
z == 0.3 // true !!!  

在java中试一下???
```

在 JavaScript 中，分号是可选的 ，自由度太高，并不好，反而会出现莫名其妙的失误，和那些具有严格类型检查和使用规范的语言的差异。


## JavaScript 不支持使用名字来索引数组，只允许使用数字索引
```js
var person = [];
person[0] = "John";
person[1] = "Doe";
person[2] = 46;
var x = person.length;         // person.length 返回 3
var y = person[0];             // person[0] 返回 "John"
```
如果你使用名字作为索引，当访问数组时，JavaScript 会把数组重新定义为标准对象。
执行这样操作后，数组的方法及属性将不能再使用，否则会产生错误。

这种操作本身是不可取的，因为不合规范，发生的错误不好评估，需要根据原理去分析，下面可以去调试观察一些person的类型状态，没搞明白...??
```js
var person = [];
person["firstName"] = "John";
person["lastName"] = "Doe";
person["age"] = 46; 
	debugger
	console.log( JSON.stringify(person));
person = {a:"aa",b:"vv"};
	debugger
	console.log( JSON.stringify(person));
document.getElementById("demo").innerHTML = JSON.stringify(person);
```
在每个代码块中 JavaScript 不会创建一个新的作用域，一般各个代码块的作用域都是全局的。函数内部则是局部变量，函数退出后销毁。

```js
for (var i = 0; i < 10; i++) {
    // some code
}
return i;
//上述代码 java中试一下。
```

input的required属性，表单自动验证。ie9较早版本不支持。

服务端数据验证是在数据提交到服务器上后再验证。
客户端数据验证 side validation是在数据发送到服务器前，在浏览器上完成验证。

通过form属性 onsubmit="return validateForm()"进行表单验证，这里使用""，根据返回值决定是否触发action的调用????

js 表单验证，通过dom方法和dom属性，validity。

## ECMAScript
ECMAScript 5（ES5，2009 年发布），是 JavaScript 最新的官方版本。

## javascript object notation
JSON.parse()	用于将一个 JSON 字符串转换为 JavaScript 对象。
JSON.stringify()	用于将 JavaScript 值转换为 JSON 字符串。

## href超链接，
1. 具体地址
2. herf = #id页面定位
3. 死链接或者执行一段代码
```js
"javascript:void(0)" 
"javascript:void(...code...)" 
```

## 大小写问题
1. 大多 Web 服务器 (Apache, Unix) 对大小写敏感
2. 其他 Web 服务器 (Microsoft, IIS) 对大小写不敏感

函数当作对象
var x = function (a, b) {return a * b};
var z = x(4, 3);
上述是匿名函数。
函数定义
function myFunction(a, b) {
    return a * b;
}
Function构造器//构造器参数？???前面都是参数，最后面是函数体??
var myFunction = new Function("a", "b", "return a * b");

var x = myFunction(4, 3);


在 JavaScript 中，很多时候，你需要避免使用 new 关键字。

函数定义作为对象的属性，称之为对象方法。
函数如果用于创建新的对象，称之为对象的构造函数。

JavaScript 函数定义时显示参数没有指定数据类型。
JavaScript 函数对隐式参数没有进行类型检测。
JavaScript 函数对隐式参数的个数没有进行检测。

JavaScript 函数有个内置的对象 arguments 对象。
argument 对象包含了函数调用的参数数组。

x = sumAll(1, 123, 500, 115, 44, 88);
 
function sumAll() {
    var i, sum = 0;
    for (i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}

js的参数传递
通过值传递参数
在函数中调用的参数是函数的隐式参数。
JavaScript 隐式参数通过值来传递：函数仅仅只是获取值。
如果函数修改参数的值，不会修改显式参数的初始值（在函数外定义）。
隐式参数的改变在函数外是不可见的。

通过对象传递参数
在JavaScript中，可以引用对象的值。
因此我们在函数内部修改对象的属性就会修改其初始的值。
修改对象属性可作用于函数外部（全局变量）。
修改对象属性在函数外是可见的。

构造函数中 this 关键字没有任何的值。
this 的值在函数调用时实例化对象(new object)时创建。


#js闭包没有看明白
http://www.jb51.net/article/24101.htm
闭包，理解。函数内部嵌套，返回内部函数，内部函数控制外部函数局部变量，从而实现从外部函数外对其内部变量的控制。
延长了变量在内存的生存周期，不宜过多使用。
这里使用闭包的目的仅仅是为了外部对内部变量控制，内部变量的安全性，常用例子的效果也就是一般编程静态变量的效果，？java中闭包？类似内部类？另外一个函数就是一个闭包？闭包的学术定义？
关于闭包引发的思考，涉及语言机理，语法规则以及运行机制，如js的垃圾回收，解释器运行原理。

#js的prototype
对象方法，类方法，原型方法。
http://www.cnblogs.com/yjf512/archive/2011/06/03/2071914.html


js操作html的dom对象
通过 id 找到 HTML 元素
通过标签名找到 HTML 元素
通过类名找到 HTML 元素
获取对应元素element后可以修改标签内容或者属性。
改变内容，innerHtml
改变属性，使用属性值，如修改图片src
改变css样式，element.style.属性 = 
document.getElementById("p2").style.color="blue";
document.getElementById("p2").style.fontFamily="Arial";
document.getElementById("p2").style.fontSize="larger";

对事件做出反映，比如onclick="js代码",可以添加this参数

e.onclick = 某函数，进行事件分配。

onload 和 onunload 事件会在用户进入或离开页面时被触发。
onload 事件可用于检测访问者的浏览器类型和浏览器版本，并基于这些信息来加载网页的正确版本。
onload 和 onunload 事件可用于处理 cookie。
body标签添加onload响应。
onchange 事件常结合对输入字段的验证来使用。对input的value进行校验。
在 JavaScript 中，document.write() 可用于直接向 HTML 输出流写内容。加载完后执行此函数会覆盖html。

设置事件监听，一个元素可以添加多个事件监听，同一个事件类型可以多次添加监听器，都有效。
document.getElementById("myBtn").addEventListener("click", displayDate);

element.addEventListener(event, function, useCapture);
useCapture是捕获标志？捕获和冒泡？事件传递原理-----捕获，外部先触发，冒泡，内部先触发，默认是冒泡，useCapture为false。
对window对象添加事件监听，比如resize事件。
传递参数的时候，使用"匿名函数"调用带参数的函数。如果是无参函数，可以使用函数名，又参数需要利用匿名函数包裹对应函数，否则设置的时候就会触发函数。

ji关于dom节点的增删
如果能够在不引用父元素的情况下删除某个元素，就太好了。
不过很遗憾。DOM 需要清楚您需要删除的元素，以及它的父元素。
这是常用的解决方案：找到您希望删除的子元素，然后使用其 parentNode 属性来找到父元素

JavaScript 类
JavaScript 是面向对象的语言，但 JavaScript 不使用类。
在 JavaScript 中，不会创建类，也不会通过类来创建对象（就像在其他面向对象的语言中那样）。
JavaScript 基于 prototype，而不是基于类的。

所有 JavaScript 数字均为 64 位
JavaScript 不是类型语言。与许多其他编程语言不同，JavaScript 不定义不同类型的数字，比如整数、短、长、浮点等等。
JavaScript 中的所有数字都存储为根为 10 的 64 位（8 比特），浮点数。