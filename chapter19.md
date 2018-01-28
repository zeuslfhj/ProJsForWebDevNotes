# E4X

## 简介

- 原生的支持XML
- ECMAScript语言的可选扩展
- 为处理XML定义了新的语法，也定义了特定于XML的对象。

## 兼容性

- 仅FF提供了兼容（业界劳模啊）

## 类型

* XML：XML结构中的任何一个独立的部分。
    - 用于表现XML结构中任何独立的部分，包括元素、特性、注释、处理指令或文本节点。
    - 继承自Object, ```foo.toXMLString()```可以输出xml字符串
    - 可以传入字符串，DOM文档或节点 ```var x = new XML("<employee position=\"Software Engineer\"><name> Nicholas " + "Zakas</name></employee>");```
    - 类似JSX的使用 ```var employee = <employee position="Software Engineer"><name>Nicholas C. Zakas</name></employee>;```

* XMLList：XML对象的集合。
    - XML对象的集合
    - 构造方式
    
    ```
    var list = new XMLList();
    var list = new XMLList("< item/>< item/>");
    var list = < item/> + < item/>;
    var list = <>< item/>< item/></>;
    ```

    - ```fooList[0]; //获取集合中的制定元素```
    - ```fooList.length(); //获取集合的元素数量```

* Namespace：命名空间前缀与命名空间URI之间的映射。
    - 构建命名空间: ```var wrox = new Namespace("wrox", "http:// www.wrox.com/");```
    - 获取命名空间前缀: ``` wrox.prefix;```
    - 获取命名空间URI: ``` wrox.uri```

* QName：由内部名称和命名空间URI组成的一个限定名。
    - var wroxMessage = new QName(wrox, "message"); //表示" wrox:message"

## 使用方法

* 获取属性: ```console.log(employee.name);```
* 获取所有子元素: ```employees.*```  组合技: ```employees.*[0].name```
* 获取子元素

```
var firstChild = employees.child(0); //与 employees.*[ 0] 相同 
var employeeList = employees.child("employee"); //与 employees.employee 相同
var allChildren = employees.child("*"); //与 employees.* 相同
var allChildren = employees.children(); //与 employees.* 相同
```

* 属性getter & setter

```
// GETTER
employees.employee[0].@position; //"Software Engineer"
employees.employee[0].child("@position"); //"Software Engineer"
employees.employee[0].attribute("position"); //"Software Engineer"

var atts1 = employees.employee[0].@*;  // 获取所有属性
var atts2 = employees.employee[0].attributes(); // 获取所有属性

// SETTER
employees.employee[0].@position = "Author";
```

## 辅助类型的节点

- 辅助的节点类型有："text"、"element"、"comment"、"processinginstruction"或"attribute"。
- 使用```nodeKind()```获取节点类型

## 节点的查询

```
var allNames = employees..name; //取得作为<employees/>后代的所有<name/>节点

var allDescendants = employees.descendants();//所有后代节点
var allNames = employees.descendants("name");//后代中的所有<name/>元素

var allAttributes = employees..@*; //取得所有后代元素中的所有特性
var allAttributes2 = employees.descendants("@*"); //同上

var allAttributes = employees..@position;//取得所有position特性
var allAttributes2 = employees.descendants("@position");//同上

var salespeople = employees.employee.(@position == "Salesperson");

employees.employee.(@position == "Salesperson")[0].@position= "Senior Salesperson";

var employees2 = employees.employee.parent();
```

## 使用方式
```
var tagName = "color";
var color = "red";
var xml = <{tagName}>{color}</{tagName}>; // 自动替换为变量 <color>red</color>
```

#### 方法
- appendChild(child)：将给定的child作为子节点添加到XMLList的末尾。
- copy()：返回XML对象副本。
- insertChildAfter(refNode,child)：将child作为子节点插入到XMLList中refNode的后面。
- insertChildBefore(refNode,child)：将child作为子节点插入到XMLList中refNode的前面。
- prependChild(child)：将给定的child作为子节点添加到XMLList的开始位置。
- replace(propertyName,value)：用value值替换名为propertyName的属性，这个属性可能是一个元素，也可能是一个特性。
- setChildren(children)：用children替换当前所有的子元素，children可以是XML对象，也可是XMLList对象。

#### for-each-in循环
```
for each(var attribute in employees.@*){ //遍历特性
    alert(attribute);
}
```

## 如何启用
```<script type="text/javascript;e4x=1" src="e4x_file.js"></script>```

