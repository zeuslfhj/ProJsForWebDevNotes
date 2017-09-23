# 第七章函数表达式

#### 函数的定义方式
- 函数声明，其包含的一个重要特性就是函数声明提升(function declaration hoisting)，即执行代码前会先读取函数声明
- 函数表达式，函数表达式一般没有具体的函数名称

```javascript
// 函数声明
foo1(); // 由于函数声明提升的特性，这句并不会报错
function foo1() {
    alert('declaration');
}

// foo2(); // 执行异常，因为foo2的函数柄没有被创建
var foo2 = function(){
    alert('expression');
}

// 注意事项
function b() {
    x(); // !!!x函数表达式，而不是函数声明，因此这里执行x的话会是x not defined
    (function x(){alert(1);});
}
```

- 下列方法声明的方式在大多数浏览器中可能会直接返回第二个，但实际在node & firefox & chrome & safari中尝试后，node & chrome & firefox是根据condition的条件来创建不同的方法，与表达式的声明结果一致，但是safari只会调用第二个方法，因此使用时需要特别注意
```
var condition = false;

if (condition) {
    function sayHi() {
        console.log('Hi');
    }
} else {
    function sayHi() {
        console.log('hello');
    }
}
```

### 递归调用
- 构建方式一，下列调用在node环境下运行正确，不过chrome下运行报错
```javascript
function factorial(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * factorial(num - 1);
    }
}

var anotherFactorial = factorial;
factorial = null;
anotherFactorial(4); // 出错，factorial实际已经被摧毁儿不存在
```

- 构建方式二，使用callee构建递归调用，但严格模式下callee并不能通过脚本访问，因此会报错
```javascript
function factorial(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * arguments.callee(num - 1);
    }
}
```

- 构建方式三，使用函数表达式
```javascript
var factorial = (function f(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * f(num - 1);
    }
})
```

### 闭包
- 书上表达
    - 闭包是有权访问另一个函数作用域中的变量的函数
    - 常见方式一个函数内部创建另一个函数
- MDN定义（lexical environment
    - A closure is the combination of a function and the lexical environment within which that function was declared.
- 个人理解：闭包是作用域链 + 函数本身的组合，作用域链是其中的一个部分

- 典型的误解：闭包存储的是VO指针而不是某个变量的值或指针，因此下方返回的使用时VO中i的值10，而不是创建方法时的1～10
```javascript
function createFunctions() {
    var result = new Array();
    for (var i = 0; i < 10; i++) {
        result[i] = function() {
            return i;
        };
    }
    return result;
}
```
- 为了返回1～10，可以使用另一种方式来创建闭包
```javascript
function createFunctions() {
    var result = new Array();
    for (var i = 0; i < 10; i++) {
        result[i] = function(num){
            return function() {
                return num;
            };
        }(i);
    }
    return result;
}
```

### this对象
- this对象是在运行时基于函数的执行环境绑定的。
- 匿名函数的执行环境具有全局性，因此this通常执行window(通过call or apply or bind便会有所不同)
- 由于编写闭包的方式不同，需要特别注意一下
- 内部函数在搜索this & arguments时，只会搜索到其活动对象为止，因此永远不可能直接访问外部函数中的这两个变量。不过，把外部作用域中的this对象保存在一个闭包能够访问到的变量里，就可以让闭包访问该对象了
- 下列不同的调用方法会引起不同的this值
```javascript
var name = 'The Window';
var object = {
    name: 'My Object',
    getName: function () {
        return this.name;
    }
}
object.getName(); //"My Object"
(object.getName)(); //"My Object"
(object.getName = object. getName)(); //"The Window"，
object.getName(); //"My Object"
```

### 内存泄漏
- 典型的泄漏，element元素持有闭包的引用，而闭包持有assignHandler的VO，VO中含有element元素的引用，引起循环引用，内存无法被正确释放
```javascript
function assignHandler() {
    var element = document.getElementById(" someElement");
    element.onclick = function() {
        alert(element.id);
    };
}
```

修改后

```javascript
function assignHandler() {
    var element = document.getElementById(" someElement");
    var id = element.id;
    element.onclick = function() {
        alert(id);
    };
    element = null;
}
```

### 闭包的应用
#### 模仿块级作用域
```javascript
function outputNumbers(count) {
    (function() {
        for (var i = 0; i < count; i++) {
            alert(i);
        }
    })();
    alert(i); //导致一个错误！ i不可被访问
}
```
#### 私有变量
- 私有变量

```javascript
function MyObject() {
    //私有变量和私有函数 
    var privateVariable = 10;
    //特权方法
    function privateFunction() {
        return false;
    }
    this.publicMethod = function() {
        privateVariable++;
        return privateFunction();
    };
}
```

- 静态私有变量

```javascript
(function() {
    //私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
        return false;
    } 
    //构造函数
    MyObject = function() {}; 
    //公有/特权方法
    MyObject.prototype.publicMethod = function() {
        privateVariable++;
        return privateFunction();
    };
})();
```
- 模块模式

```javascript
var singleton = function() {
    //私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
        return false;
    }
    //特权/公有方法和属性
    return {
        publicProperty: true,
        publicMethod: function() {
            privateVariable++;
            return privateFunction();
        }
    };
}();
```
- 增强的模块模式: 增强模式和普通的区别是在于加入了类型的限制，例如下列代码中的模块必须是BaseComponent的实例对象

```javascript
var application = function() {
    //私有变量和函数 
    var components = new Array();
    //初始化 
    components.push(new BaseComponent());
    //创建application的一个局部副本 
    var app = new BaseComponent();
    //公共接口 
    app.getComponentCount = function() {
        return components.length;
    };
    app.registerComponent = function(component) {
        if (typeof component == "object") {
            components.push(component);
        }
    };
    //返回这个副本 
    return app;
}();
```

#### Context

- **this**
> When called as an unbound function, this will default to the global context or window object in the browser. However, if the function is executed in strict mode, the context will default to undefined.
- **closure**:
> A closure is the combination of a function and the lexical environment within which that function was declared.

#### Variable Object

- A variable object (in abbreviated form — VO) is a special object related with an execution context and which stores:
    - variables (var, VariableDeclaration);
    - function declarations (FunctionDeclaration, in abbreviated form FD);
    - and function formal parameters
> Notice, in ES5 the concept of variable object is replaced with lexical environments model.

#### Variable object in function context

- Regarding the execution context of functions — there VO is inaccessible directly, and its role plays so-called an activation object (in abbreviated form — AO).

> In ES5 the concept of activation object is also replaced with common and single model of lexical environments.

- Arguments object contains the following properties:
    - callee — the reference to the current function;
    - length — quantity of real passed arguments;
    - properties-indexes (integer, converted to string) which values are the values of function’s arguments. Quantity of these properties-indexes == arguments.length. Values of properties-indexes of the arguments object and present (really passed) formal parameters are shared.

```javascript
function foo(x, y, z) {
  
  // quantity of defined function arguments (x, y, z)
  alert(foo.length); // 3
 
  // quantity of really passed arguments (only x, y)
  alert(arguments.length); // 2
 
  // reference of a function to itself
  alert(arguments.callee === foo); // true
  
  // parameters sharing
 
  alert(x === arguments[0]); // true
  alert(x); // 10
  
  arguments[0] = 20;
  alert(x); // 20
  
  x = 30;
  alert(arguments[0]); // 30
  
  // however, for not passed argument z,
  // related index-property of the arguments
  // object is not shared
  
  z = 40;
  alert(arguments[2]); // undefined
  
  arguments[2] = 50;
  alert(z); // 40
  
}
  
foo(10, 20);
```

#### About Variable
- variables are declared only with using var keyword. assignments like ```a = 10;``` just create the new property (but not the variable) of the global object. 

```javascript
alert(a); // undefined
alert(b); // "b" is not defined
 
b = 10;
var a = 20;
```

- There is one more important point concerning variables. Variables, in contrast with simple properties, have attribute {DontDelete}, meaning impossibility to remove a variable via the delete operator (execution context of eval is not affected)

> in ES5 {DontDelete} is renamed into the [[Configurable]] and can be manually managed via Object.defineProperty method.

```javascript
a = 10;
alert(window.a); // 10
 
alert(delete a); // true
 
alert(window.a); // undefined
 
var b = 20;
alert(window.b); // 20
 
alert(delete b); // false
 
alert(window.b); // still 20
```

> References
> http://dmitrysoshnikov.com/ecmascript/chapter-2-variable-object/
