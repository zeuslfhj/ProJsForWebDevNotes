# Javascript历史

ECMA-262标准又称为***ECMAScript***，发音为ek-ma-script，规定了下列组成部分, Javascript只是其中的一个实现，ActionScript也是该标准的实现之一

javascript实现由下列三个不同部分组成
- ECMAScript(ECMA-262)：核心
  + 语法
  + 类型
  + 语句
  + 关键字
  + 保留字
  + 操作符
  + 对象
- DOM：文档对象模型，Netscapet和微软的相争，引发了W3C协会开始规划DOM标准
- BOM：浏览器对象模型

#### ECMA修订历史
ECMA-262第4版做了大幅度修改，强类型变量，新语句，新数据结构，真正的类和继承
ES3.1作为小幅度的修改

# 基础知识
```javascript
function(){
    message = 1;
}

// var a = 1;
alert(message);
alert(a); // 该条语句会报

// 严格模式下会报错，message被设定为了全局变量
```

```javascript
var message;
message === undefined; // true
// var a;
typeof message; // 输出为undefined
typeof a; // output is undefined，虽然该变量未被声明，但执行typeof的确会输出undefined，且不会报错

```

```javascript
typeof null; // output is object
```

```javascript
null == undefinded; // true
```

## 数据类型

#### Boolean类型
可以通过Boolean()对其他类型的值进行转换，结果为true or false

#### Number类型
- 8进制：以0开头，后面的数字为0~7，严格模式下报错，例如07
- 16进制: 以0x开头，后面的数字为0~F，大小写不限, 0x2f
- e表示法：表示极大极小值，e后的数字为10的指数次幂, 例如
  + 正指数表示：```var a = 3.125e7; //实际值为31250000```
  + 负指数表示：```var a = 1e-3; //实际值为0.001```
- 浮点数
  + 最高精度为17位小数 
  + 计算精度不如小数
  + 基于 IEEE754 数值的浮点数计算会有舍入误差 ***可以考虑重点聊一下***
    + ``` 0.1 + 0.2; // 输出为 0.30000000000000004;```
    + ``` 0.25 + 0.05; // 输出为 0.3 ```
- Infinity 无穷值无法参与计算可以使用```isFinite()```对当前值进行判断
- isNaN 用于判断当前是否是非数字
    + ```NaN == NaN; //false```
    + 只能使用isNaN来判断是否是NaN
    + isNaN除了可以用来判断NaN本身以外，还可用用来判断字符串等内容
```javascript
isNaN(NaN); //true
isNaN(10); //false
isNaN("10"); //false 可以转为（10）
isNaN("blue"); //true
isNaN(true); // true (可以转为1)
```
- parseInt转换字符串的规则
    - 忽略字符串前面的空格，直至找到第一个非空格字符。
    - 如果第一个字符不是数字字符或者负号，返回NaN；
    - 第一个字符是数字字符，parseInt()会继续解析第二个字符，直到解析完所有后续字符或者遇到了一个非数字字符。
    - 例如```parseInt('22.5'); // 结果是22，小数点并非有效的字符```
    - 通过传递第二个参数，支持二进制，八进制，十进制（默认），十六进制转换
- parseFloat只解析十进制

#### String类型
- 字符串的值不可变，因此每次声明一个String都在内存中创建一个新的内存地址用于存放新的字符串
- 改变某个字符串的值时便会销毁当前的字符串值，然后创建一个新的字符串值填充到变量中
```javascript
var lang = 'java';
lang = lang + 'Script';
```
- 上面的代码首先创建字符串java以及Script，然后申明一个长度为10的新字符串空间，将java与Script放入，最后销毁java以及Script。
- 循环或者多次字符串拼接的话可以考虑用array的join，可以避免中间的多次中间状态字符串的创建
- 在Number类型的值中调用toString()方法中，带入基数可以输出不同进制格式的值```(15).toString(16); // output is f```
- 可以使用String()或者 **+""** 转换不安全的内容，例如
```javascript
var a;
// a.toString(); // 该语句执行会报错
String(a); //输出undefined
a+"";      //输出undefined
```

## 操作符
- 操作符：注意NaN及Infinite的计算结果
- false默认转换为0，true转换为1
- 非数字的会调用valueOf进行转换，如果没有该方法则先调用toString，然后再调用valueOf
- 注意字符串的比较运行，根据ASCII值进行比较```'23' < '3'; //true```
```javascript
null == undefined; //true
null === undefined; //false
```
- ==运算符的特殊结果

表达式 | 值 | 表达式 | 值 |
---|---|---|---
null == undefined | true | null === undefined | false
"NaN" == NaN | false | true == 2 | false
5 == NaN | false | undefined == 0 | false
NaN == NaN | false | NaN === NaN | false
NaN != NaN | true  | NaN !== NaN | true 
false == 0 | true  | false === 0 | false
true == 1 | true | true === 1 | false
null == 0 | false | "5" == 5 | true

## 语句

#### for循环
- for循环在es6中的作用域, let让i的作用域限定在for循环中，而var则在外部也可以访问

```javascript
for(let i = 1; i < 10; i++){}console.log(i); // undefined
for(var i = 1; i < 10; i++){}console.log(i); // output is 10
```


#### label表达式
- 与break及continue在循环中搭配使用

#### with表达式
- 将代码块作用域设置到一个特定的对象中

```javascript
// 不使用with语句
var qs = location.search.substring(1);
var hostName = location.hostName;

// 使用with语句
with(location){
    var qs = search.substring(1);
    var hostName = hostName;
}
```

- 性能低下
- 严格模式中视为语法错误

#### switch
- switch使用的是全等表达式
 

## 函数

#### arguments
- arguments与参数之间的关系，修改arguments或者参数变量的值在非严格模式下会产生互相影响

```javascript
// 'use strict'
// 严格模式下的行为与非严格模式不同
// 严格模式下不会互相影响，但是非严格模式会
function foo(a, b){
  console.log(arguments[0]);
  arguments[0] = 'b';
  console.log(a);
  console.log(arguments[0]);
  a = 'd';
  
  console.log(a);
  console.log(arguments[0]);
}

foo('a', 'c');
```