---
title: JavaScript深入 - 原型与原型链
date: 2018-4-26 
tags: JavaScript
categories: JavaScript
---



`今天看了github上面的某位大佬对原型与原型链的文章 讲解很透彻` 

[原型与原型链]( https://github.com/mqyqingfeng/Blog/issues/2 )

### prototype

每个函数都有prototype属性,如下

![](http://on7r0tqgu.bkt.clouddn.com/FlhjUzcXSi9VgzTXxoiITVxR7lMj.png)

这个函数的prototype到底是什么呢,是函数的原型吗?

其实,函数的prototype都会指向一个对象,这个对象 就是调用改构造函数而创建的`实例的原型`,也就是上图的a b的原型

什么是原型呢?

`每一个JavaScript对象(null除外)在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型"继承"属性。`

####用过一张图来表示构造函数与实例之间的关系

![](https://github.com/mqyqingfeng/Blog/raw/master/Images/prototype1.png)

那么上面的变量 ab是怎么访问到score的prototype.name的呢?

这里就要提出第二个属性

#### ___ proto ___

这是每个JavaScript对象都会有的属性 叫做_ proto _ 这和属性 指向该对象的原型 即是

 ```JavaScript
function score () {}
score.prototype.name = "张三"
var b= new score()
b.__proto__.name   //"张三"
 ```

这里可以看到 b._ proto_ 是指向score.prototype的 或者我们可以证明

```
score.prototype == b.__proto__  //true
```

现在我们知道

构造函数 prototype 指向原型  构造函数可以生成实例  实例的 _ proto _ 指向原型 

于是关系图可以这么理解

![](https://github.com/mqyqingfeng/Blog/raw/master/Images/prototype2.png)

这里构造函数 和 实例都指向 原型 那么会不会有 原型指向构造函数与实例呢?

 #### constructor

没有指向实例的方法 因为 会有多个实例 但是有指向构造函数的方法 就是constructor `每个原型都有一个constructor属性指向关联的构造函数`

![](http://on7r0tqgu.bkt.clouddn.com/FuZBPzjSstr5QotCzB-qLIZlkrJK.png)



可以看出来constructor指向构造函数本身

总结一下 现在得出结论

![](http://on7r0tqgu.bkt.clouddn.com/FsyQCad9cdtMS9wJQ6FFKGpNNRw_.png)3

```
console.log(score.prototype.constructor == score)
console.log(score.prototype == a.__proto__)
```

再更新一下图

![](https://github.com/mqyqingfeng/Blog/raw/master/Images/prototype3.png)

#### 实例与原型

`当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层为止。`

```
function score () {}
score.prototype.age="20"    //在原型链上面赋值 age = 20
var a = new score()
a.age
"20"      //age指向原型链上面
a.age= "10" 
"10"      //这时候指向a实例上面
delete a.age;
a.age
"20"     //age指向原型链
```

当实例没有age这个属性 他会在原型链上面找

当给实例赋值的时候 就会覆盖prototype上面的属性 但是 prototype上面的属性依旧存在

当将实例上面的age删除的时候 就会依旧打印出原型链上面的属性

这里思考一个问题 JavaScript万物皆对象 name prototype的对象是什么呢?

####原型的原型

 我们已经讲了原型也是一个对象，既然是对象，我们就可以用最原始的方式创建它

```JavaScript
Object.prototype.name = "张三"
var obj = new Object();
console.log(obj.name) // 张三   //和上面同理 来自原型链上面的name
Object.prototype.constructor == Object
```

现在关系图再次更新

![](https://github.com/mqyqingfeng/Blog/raw/master/Images/prototype4.png)

#### 原型链

那 Object.prototype 的原型呢？

```
console.log(Object.prototype.__proto__)
null
```

null 表示“没有对象”，即该处不应该有值

所以 Object.prototype.__proto__ 的值为 null 跟 Object.prototype 没有原型，其实表达了一个意思。

最后整理关系图

![](https://github.com/mqyqingfeng/Blog/raw/master/Images/prototype5.png)

#### 图中由相互关联的原型组成的链状结构就是原型链，也就是蓝色的这条线。

最让人不注意的就是日常引用要注意的问题

首先 看下面代码

```JavaScript
function score () {}
var a = new score()
console.log(a.constructor == score)
```

答案是 true

当获取 a.constructor 时，其实 a中并没有 constructor 属性,当不能读取到constructor 属性时，会从 a的原型也就是 score.prototype 中读取，正好原型中有该属性，所以相当于: 

```javascript
person.constructor === Person.prototype.constructor
```

学过java的人可能会将JavaScript的原型链理解为继承 但是 JavaScript里面一直以来都没有这个说法 

`你不知道的JavaScript` 里面有一句话 

**继承意味着复制操作，然而 JavaScript 默认并不会复制对象的属性，相反，JavaScript 只是在两个对象之间创建一个关联，这样，一个对象就可以通过委托访问另一个对象的属性和函数，所以与其叫继承，`委托`的说法反而更准确些。**





ps:但是难度有点高,讲的都是偏原理的东西,一段时间不看总是容易忘记,根本原因是对JavaScript原型链实际应用太少了,温故而知新吧







