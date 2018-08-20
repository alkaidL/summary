# 二十二 XML解析

XML，即 `extensible Markup Language` ，是一种**数据标记语言** & **传输格式** 

作用：对数据进行标记（结构化数据）、存储 & 传输 

## XML语法

- 元素要关闭标签
   `< p >this is a bitch <p>` 
- 对大小写敏感

```xml
< P >这是错误的<p>
< p >这是正确的<p>
```

- 必须要有根元素(父元素）

```xml
<root>
   <kid>
   </kid>
</root>
```

- 属性值必须加引号

```xml
<note date="16/08/08">
</note>
```

- 实体引用

| 实体引用 | 符号 | 含义    |
| -------- | ---- | ------- |
| &lt；    | <    | 小于    |
| &gt ；   | >    | 大于    |
| &amp；   | &    | "and"符 |
| &apos；  | ‘    | 单引号  |
| &quot；  | "    | 双引号  |

元素不能使用&(实体的开始）和<（新元素的开始）

- XML的元素、属性和属性值

文档实例

```xml
<bookstore>
  <book category="CHILDREN">
     <title lang="en"> Harry Potter </title>
     <author> JK.Rowling</author>
  </book>
<book category="WEB">
     <title lang="en"> woshiPM </title>
     <author>Carson_Ho</author>
  </book>
</bookstore>
```

其中，\<bookstore>是根元素；\<book>是子元素，也是元素类型之一；而\<book>中含有属性，即category，属性值是CHILDREN；而元素\<author>则拥有文本内容（ JK.Rowling）

- 元素与属性的差别
  属性即提供元素额外的信息，但不属于数据组成部分的信息。

范例一

```xml
<bookstore>
  <book category="CHILDREN">
     <title lang="en"> Harry Potter </title>
     <author> JK.Rowling</author>
  </book>
```

范例二

```xml
<bookstore>
  <book >
     <category>CHILDREN<category>
     <title lang="en"> Harry Potter </title>
     <author> JK.Rowling</author>
  </book>
```

范例一和二提供的信息是完全相同的。

一般情况下，请使用元素，因为

1. 属性无法描述树结构（元素可以）
2. 属性不容易拓展（元素可以）

使用属性的情况：用于**分配ID索引**，用于标识XML元素。

> 实例

```xml
<bookstore>
  <book id = "501">
     <category>CHILDREN<category>
     <title lang="en"> Harry Potter </title>
     <author> JK.Rowling</author>
  </book>
  <book id = "502">
     <category>CHILDREN<category>
     <title lang="en"> Harry Potter </title>
     <author> JK.Rowling</author>
  </book>
<bookstore>
```

上述属性（id）仅用于标识不同的便签，并不是数据的组成部分

- XML元素命名规则
  1. 不能以数字或标点符号开头
  2. 不能包含空格
  3. 不能以xml开头

- CDATA
   不被解析器解析的文本数据，所有xml文档都会被解析器解析（cdata区段除外）
   `<![CDATA["传输的文本 "]]>`
- PCDATA
   被解析的字符数据

## XML树结构

- 假设一个XML文件如下

```
<？xml version ="1.0" encoding="UTF-8"?>
<简历>
     <基本资料>
     <求职意向>
     <自我评价>
     <其他信息>
     <联系方式>
     <我的作品>
 </简历>
```

​	其树结构如下
![img](https://upload-images.jianshu.io/upload_images/944365-b6f9ca5ebe95c00a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

- XML节点解释 XML文件是由节点构成的。它的第一个节点为“根节点”。一个XML文件必须有且只能有一个根节点，其他节点都必须是它的子节点。this 代表整个XML文件，它的根节点就是 this.firstChild

![img](https://upload-images.jianshu.io/upload_images/944365-3a4ed8db15602770.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/538) 

## 常用解析方式

### 基于文档驱动

原理：在解析XML文档之前，需先将整个XML文件存储到内存中

#### DOM方式

![img](https://upload-images.jianshu.io/upload_images/944365-5aceda236a0d44c2.png) 

### 基于事件驱动

原理：根据不同需求事件（检索、修改、删除等）去执行不同解析操作

#### SAX方式

![img](https://upload-images.jianshu.io/upload_images/944365-4eebf15fb06a2f07.png) 

#### PULL方式

![img](https://upload-images.jianshu.io/upload_images/944365-297fb5d8c529a53e.png) 

### 三种方式对比

![img](https://upload-images.jianshu.io/upload_images/944365-3434ec5de8ad1f14.png) 