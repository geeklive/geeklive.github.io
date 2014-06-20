---
layout: post
title: 《JavaScript DOM 编程艺术》读书笔记
description: 随着Node.js的崛起，各大互联网公司对前端工程师的渴求可谓疯狂。按照这个节奏，前端工程师很有可能发展为产品技术工程师，向上对接BD、运营、视觉等，向下对接JAVA、DBA、PE等。作为一个见习PM，个人感觉了解前端诸多技能对交互感知会有不少提升，所以开始前端技能的加点，第一站——《Javascript Dom 编程艺术》。
category: blog
---

## 第1章 JavaScript简史

* BOM浏览器对象模型，允许访问和操控浏览器窗口。
* DOM文件对象模型，是一套对文档的内容进行抽象和概念化的方法。
* DHTML动态HTML，结合HTML、CSS、JavaScript三种技术。
* W3C推出的标准化DOM可以让任何一种程序设计语言对使用任何一种标记语言编写出来的任何一份文档进行操纵。
* DOM是一种API（应用编程接口）。API：一组已经得到有关各方共同认可的基本约定（相当于现实世界的莫尔斯码、元素周期表）。
* W3C对DOM的定义是：一个与系统平台和编程语言无关的接口，程序和脚本可以通过这个接口动态地对文档的内容、结构和样式进行访问和修改。

## 第2章 JavaScript语法

* 相等操作符==认为空字符串与false含义相同；全等操作符===会严格比较(值与类型)；
* 在函数中对与全局变量相同名字的变量使用var关键字控制其为局部变量，因此函数内部变量应都使用var定义；
* JavaScript 中的对象分为三种类型：用户定义的对象(`user-defined object`)；内建对象(`native object`)内建在JavaScript语言里的对象，如Array、Math和Date等；宿主对象(`host object`)由浏览器提供的对象。

## 第3章 DOM

* `getElementById` 返回对象，`getElementsByTagName` 返回对象数组；
* 现有DOM方法实现 `getElementsByClassName` :

```
     function getElementsByClassName(node,classname){
          if(node.getElementsByClassName){
               return node.getElementsByClassName(classname);
          }else{
               var results = new Array();
               var elems = node.getElementsByTagName("*");
               for(var i=0; i<elems.length; i++){
                    if (elems[i].className.indexof(classname) != -1){
                         results[results.length] = elems[i];
                    }
               }
          return results;
          }
     }
```
* `getAttribute()` ，`setAttribute()` ；

## 第4章 实例研究：JavaScript美术馆

* `childNodes` 属性：获取任何元素的所有子元素数组；
* `nodeType` 属性：元素节点 `nodeType` 属性值：1；属性节点：2；文本节点：3；
* `nodeValue` 属性：改变一个文本节点的值，`node.childNodes[0].nodeValue` ；
* `firstChild,lastChild` 属性：

```
     node.childNodes[0] = node.firstChild；
     node.childNodes[node.childNodes.length-1] = node.lastChild；
```

## 第5章 JavaScript编程原则和良好习惯

* window.open(url,name,features)：创建新的浏览器窗口；
* 平稳退化；
* 性能考虑：<br/>
a. 尽量少访问DOM和尽量减少标记；<br/>
b. 合并和放置脚本(把所有<script>标签放于文档末尾);<br/>
c. 压缩脚本(xx.min.js)；
