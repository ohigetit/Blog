<a name="tq5kX"></a>
### 写在前面

---

开始写文章的初衷是看了周岭先生的《认知觉醒》，里面讲述的如何高效率学习，且能激励自己，莫过于有一定的产出，在此感谢[冴羽](https://github.com/mqyqingfeng)老师，以及借他认识的低调务实优秀中国好青年群友们也给前端之路上迷茫的我提供了很大的帮助。
<a name="baxZ4"></a>
## 正文

---

什么是继承？一般认知里，继承可以理解为子从父中获得其属性（我得到了我老豆的财产），而在javascipt里，继承则意味着一种关联，关联起两个对象，从而能让一个对象通过委托访问另一个对象的属性和方法。<br />如何实现继承？主要有六种方式，各有优缺点，下面会一一讲述。
<a name="VdeD4"></a>
### 1.原型链继承

---

举例说明﻿<br />给出两个构造函数<br />`function Parent(){this.name = "xiaohong";}`<br />`function Child(){}`<br />要使两者有继承关系（子承父），我们可以：<br />`Child.prototype = new Parent();`<br />创建一个实例：`var child1 = new Child();`<br />则child1可继承Parent.name<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/28546601/1652331338665-7c931bb8-b353-4f88-aa85-fccb945aec45.png#clientId=u2f88a7c8-f44a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=40&id=u23132e5f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=40&originWidth=118&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1617&status=done&style=none&taskId=uc10196fd-a048-4849-a7d7-07a8bc5823d&title=&width=118)<br />原型链继承由此实现。<br />但同时也会出现问题：<br />**（1）父中引用类型的属性会被所有创建的实例共享。**<br />我们将Parent中的name属性改为引用类型<br />`function Parent(){this.name = ["xiaohong"];}`<br />再创建一个实例：`var child2 = new Child();`<br />通过child2向name添加元素：`child2.name.push("daming");`<br />（注意：这里添加元素的方法是push，而不是child2.name = ······，直接使用child2.name = ······则是为child2创建了一个属于他自己的属性name）<br />查看child2.name<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/28546601/1652331963944-6702e09a-f009-4669-a004-4e84f8a10ee9.png#clientId=u2f88a7c8-f44a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=49&id=u3b88f6a4&margin=%5Bobject%20Object%5D&name=image.png&originHeight=49&originWidth=213&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2578&status=done&style=none&taskId=u7b137433-1eb8-48c3-8044-d21e9fa5deb&title=&width=213)<br />再查看child1.name<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/28546601/1652332017874-a6c6e30d-188c-47c8-b565-b92e3c2981cb.png#clientId=u2f88a7c8-f44a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=46&id=u112cb874&margin=%5Bobject%20Object%5D&name=image.png&originHeight=46&originWidth=205&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2487&status=done&style=none&taskId=ub7b76485-2fee-4b68-8494-ee2964aa6af&title=&width=205)<br />惊人的一致！<br />**（2）子不能向父传递参数**<br />传了也没啥用，啥都改不了

咋解决？往下看
<a name="Dqt4F"></a>
### 2.借用构造函数继承

---

同样是两个构造函数：<br />`function Parent(){this.name = "xiaohong";}`<br />`function Child(){Parent.call(this);}`<br />这里与原型链继承的差别在于：使用了call方法来实现继承，为什么说是'借用'？。<br />是因为在Child的构造函数里，我们通过call"借用"到了Parent的属性，放在了自己身上。<br />所以他叫借用构造函数继承。（读书人的事能叫偷嘛）<br />我们来验证一下：<br />创建一个实例：`var child1 = new Child();`<br />然后查看一下：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/28546601/1652333411129-6c0b429d-24e4-4bb1-aecf-b99311eb7701.png#clientId=u2f88a7c8-f44a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=42&id=u2bdb2c8d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=42&originWidth=108&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1618&status=done&style=none&taskId=u2035670a-9ef4-410b-8d85-7017314a1d6&title=&width=108)<br />那么他是否会出现和原型链继承一样的问题呢？<br />我们重复一下之前push的操作：<br />然后查看一下：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/28546601/1652333551587-a1f6167e-1e54-4924-a84d-9fa26962bce9.png#clientId=u2f88a7c8-f44a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=86&id=u34431264&margin=%5Bobject%20Object%5D&name=image.png&originHeight=86&originWidth=219&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4368&status=done&style=none&taskId=u9873deb9-922b-4059-9af6-eb6719e94c4&title=&width=219)<br />ohhhhhhh！<br />那么是否能传参呢？<br />看这里
```javascript
function Parent(name){
  this.name = name;
}
function Child(name){
  Parent.call(this,name);
}
var child1 = new Child("daming");
console.log(child1.name);//'daming'
```
当然可以！<br />但也还是会有一些问题：

1. 方法都在构造函数中定义，每次实例化对象都得创建一遍方法，基本无法实现函数复用
1. call方法仅仅调用了父级构造函数的属性及方法，没有办法调用父级构造函数原型对象的方法

于是就出现了采这二者之长的：
<a name="lEoqX"></a>
### 3.组合继承

---

就把上面这俩结合在一起呗：
```javascript
function Parent(){
			this.name = 'daming';
		}
function Child(){
			Parent.call(this,name);
		}
Child.prototype = new Parent();
		
```
就会发现这样既可以通过原型链继承父级以上的方法，又可以通过借用构造函数继承来获取属性，避免共享问题。<br />大家可以自行验证一下。<br />但他同样也有缺点：<br />每次创建实例都会调用两次父级的构造函数。<br />`Child.prototype = new Parent();`和`Parent.call(this,name);`
<a name="ze5ul"></a>
### 4.原型式继承

---

我们来定义一个创建对象的函数createObj，参数为父级object
```javascript
function createObj(obj){
			function F(){}
			F.prototype = obj;
			return new F();
		}
```
这个继承方式实际上也就是Object.create()   （ES5）<br />和原型链继承有点类似，缺点也一样。
<a name="U90cn"></a>
### 5.寄生式继承

---

原型式继承的增强版！<br />增强在哪儿？
```javascript
function parasitism(obj){
			var clone = createObj(obj);
			clone.pro = function(){
				console.log("我新增强了，多了一个方法！");
			}
			return clone;
		}
```
寄生寄生，先寄在原型式继承的身上，然后自己再生长出新的方法。<br />缺点也和借用构造函数继承类似，每创建一个实例就得创建一次方法。

<a name="QB0Ba"></a>
### 6.寄生组合式继承

---

先把上面的组合继承搬过来
```javascript
function Parent(){
			this.name = 'daming';
		}
function Child(){
			Parent.call(this,name);
		}
Child.prototype = new Parent();
		
```
前面我们说了，组合继承的缺点就是每次创建实例会调用两次父级构造函数，那么我们可以利用寄生继承来解决这个问题，即不使用 Child.prototype = new Parent() ，而是通过一个中介，间接的让 Child.prototype 访问到 Parent.prototype，这样就能减少一次父级构造函数的调用。<br />如何实现？
```javascript
function F(){}
F.prototype = Parent.prototype;
Child.prototype =new F();
```
这也是引用类型继承最理想的方式。<br />有兴趣的也可以将其封装方便后续使用。

<a name="DGO5c"></a>
### 参考

1. [JavaScript深入之继承的多种方式和优缺点](https://github.com/mqyqingfeng/Blog/issues/16#)   [冴羽](https://github.com/mqyqingfeng)
1. [6种JavaScript继承方式及优缺点](https://zhuanlan.zhihu.com/p/105312152)  [有鱼是只猫](https://www.zhihu.com/people/ha-luo-li-shi)
1. 《你不知道的javascript》
1. 《javascript高级程序设计》

<br /> 


