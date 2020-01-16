

#### 1.什么是面向对象？

​		面向对象是一种编程思想，JS本身就是基于面向对象创建出来的（例如：JS中很多内置类，像Promise就是ES6的一个内置类，我们可以new Promise来创建一个实例，来使用Promise管理异步编程），Vue/React/Jquery也是基于面向对象构建出来的，他们都是类，平时我们开发都是创建他们的实例对象来操作的。

#### 2.js继承

> 子类继承父类中的属性和方法，目的是让子类的实例能够调用父类中的属性和方法

```js
//创建类A
function A(x){
    this.x=x //其中x为实例对象的私有属性（this为new以后的实例对象）
}
A.prototype.getX=function(){
    console.log(this.x)
} //getx为实例对象的公用方法

//创建类B
function B(y){
    this.y=y
}
B.prototype.getY=function(){
    console.log(this.y)
}
let b1=new B(100)
b1.y
b1.getY()
.....
```

#### 方法一：原型继承

> 让父类中的属性和方法在子类实例的原型链上 (改变原型链的指向)
>
> 子类.prototype=new 父类 ()
>
> 子类.prototype.constructor=子类

```js
//创建父类A
function A(x){
    this.x=x 
}
A.prototype.getX=function(){
    console.log(this.x)
} 

//创建子类B
function B(y){
    this.y=y
}
B.prototype=new A(200) //原型继承
B.prototype.getY=function(){
    console.log(this.y)
}
let b1=new B(100)
b1.y
b1.getY()
//子类继承父类中的属性和方法
b1.getX()
b1.x
```

![1571903076655](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\1571903076655.png)

#### 方法二：call继承

> 子类中把父类当做普通函数执行，让父类中的this指向子类的实例，相当于给子类的实例设置了很多的私有的属性或方法
>
> - 只能继承父类私有的属性或方法
>
> - 父类私有属性变为子类私有属性

```js
function A(x){
    this.x=x
}
A.prototype.getX=function(){
    console.log(this.x)
}
function B(y){
    //=>this:B的实例b1
    A.call(this,200) //=>b1.x=x
    this.y=y
}
B.prototype.getY=function(){
    console.log(this.y)
}
let b1=new B(100)
b1.y
b1.x //=>子类继承父类的私有属性
```

#### 方法三：寄生组合继承

> 寄生组合继承：call继承+类似原型继承
>
> 特点：父类私有和公有的分别是子类实例的私有和公有属性方法（推荐）

```js
function A(x){
    this.x=x
}
A.prototype.getX=function(){
    console.log(this.x)
}
function B(y){
    //=>this:B的实例b1
    A.call(this,200) //=>b1.x=x
    this.y=y
}
//=>Object.create(obj):创建一个空对象，让空对象__proto__指向obj
B.prototype=Object,create(A.prototype)
B.prototype.constructor=B
B.prototype.getY=function(){
    console.log(this.y)
}
let b1=new B(100)
b1.y
b1.x //=>子类继承父类的私有属性
```

#### ES6中的继承

```js
class A{
    constructor(x){
        this.x=x
    }
    getX(){
        console.log(this.x)
    }
}
//=>ES6中的继承
//=>B.prototype.__proto__=A.prototype
class B extends A{
    constructor(y){
		//=>子类只要继承父类，可以不写constructor,浏览器会默认创建，一旦写了，则在constructor中的第一句话必须是super()
        super(200)//=>A.call(this,200)把父类当做普通方法执行，给方法传递参数，让方法中的this是子类的实例
        this.y=y
    }
    getY(){
        console.log(this.y)
    }
}
let b1=new B(100)
console.log(b1)
```

#### 3.js的变量提升

​		在js代码自上而下执行之前，浏览器会把带有“var/function"关键字的代码进行提前声明或定义，这种预先处理机制称之为”变量提升“。

- 声明（declare）：var a 

- 定义（defined）：a=12 （定义就是赋值）

【变量提升阶段】

- 带var的只声明未定义

- 带function的声明和赋值都完成了

  > 函数执行时会形成一个私有作用域（私有栈内存）
  >
  > 形参赋值->变量提升->代码执行

#### 4.带var和不带var

- 带var声明的是变量，同时也是给window设置了一个属性

- 不带var的本质是一个window属性

  > a=12 //=>a.window=12

```js
var a=12,
    b=13//=>这样写b是带var的
var a=b=12//=>这样写b是不带var的
```

在私有作用域中（函数作用域）中，带var和不带var的区别：

- 带var声明为私有变量，和外界没有任何关系
- 不带var非私有变量，会向上级作用域查找，不是则一直往上查找到window为止（这种查找机制称为：**作用域链**）,也就是我们在私有作用域中一直操作的是全局变量。例：

![1572419972468](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\1572419972468.png)

​	在私有作用域中，只有以下两种情况是私有变量

- 在里面声明的变量（带var/function ）
- 形参也是私有变量

剩下的都不是自己私有的变量，都需要基于作用域链向上查找

#### 5.上级作用域的查找

​		当前函数执行，形成一个私有作用域A，A的上级作用域是谁，和他在哪执行无关，和他在哪创建（定义）有关，即**在哪创建的，它的上级作用域就是谁**

```js
var a=12
function fn(){
    console.log(a)
}
function sum(){
    var a=120
    fn()   //fn的上级作用域为全局
}
sum()//=>12
```

- js中的内存分为堆内存和栈内存

​	堆内存：存储引用数据类型值（对象，函数）

​	栈内存：提供js代码执行的环境和存储基本类型值

- 【**堆内存释放**】

  ​	让所引用堆内存空间地址的变量赋值为null即可 （没有变量占用这个堆内存了浏览器会在空闲时候把它释放掉）

- 【**栈内存释放**】

  ​	一般情况下，当函数执行完成后，所形成的私有作用域（栈内存）都会自动释放掉，但也有特殊不销毁的情况：

  - 函数执行完时，当前形成的栈内存中，某些内容被栈内存以外的变量占用了，此时栈内存不能释放（一旦释放，外面找不到原有的内容）
  - 全局栈内存只有在页面关闭时才会被释放
  - 如果当前栈内存没有被释放，那么之前在栈内存中存储的基本值也不会被释放，能够一直保存下来

```JS
var n=10
function fn(){
    var n=20
    function f(){
        n++
        console.log(n)
    }
    f()
    return f
}
var x=fn()
x()
x()
console.log(n) //=>21 22 23 10
```

```js
var i=1
function fn(i){
    return function(n){
        console.log(n+(++i))
    }
}
var f=fn(2)
f(3)
fn(5)(6)
fn(7)(8)
f(4) //=>6 12 16 8
```

#### 6.闭包

> 形成一个不销毁的私有作用域就是闭包

```js
//=>闭包写法一：柯里化函数
function fn(){
    return function(){
        
    }    
}
var f=fn()

///=>闭包写法二：惰性函数
var utils=(function(){
    return {
        
    }
})()
```

##### 闭包的应用

​	真实项目中，不销毁的堆栈内存是耗性能的，为了保证js的性能，应该**尽可能的减少闭包的使用**

##### 闭包的作用

> 闭包的保护作用和保存作用

- 保护作用：保护私有变量不受外界的干扰

  ​		在团队开发一个项目时，尽可能的减少全局变量的使用，以防止相互之间的冲突（全局变量污染），那么此时我们完全可以把自己这部分的内容封装到一个闭包中，让全局变量转换为私有变量

  ```js
  (function(){
      ...
      var n=12
      function fn(){
          ...
      }
      ...
  })()
  ```

  ​	不仅如此，我们封装类库插件时，也会把自己的程序都存放到闭包中保护起来，防止和用户的程序冲突，但我们又需要暴露一些方法给用户使用，可以有下面两种方法去解决：

  - JQUERY方法：通过给window设置属性的方式暴露出去

  ```js
  (function(){
      function jQuery(){
          //...
      }
      //把需要供外面使用的方法，通过给window设置属性的方式暴露出去
      window.jQuery=window.$=jQuery
  })()
  jQuery()
  $()
  ```

  - Zepto方法：基于return把需要供外面使用的方法暴露出去

  ```js
  var Zepto=(function(){
      //...
      return {
          xxx:function(){
              
          }
      }
  })()
  Zepto.xxx()
  ```

  

- 保存作用：形成不销毁的栈内存，把一些值保存下来，方便后面的调取使用

#### 7.构造函数的执行机制

 f=new Fn()的执行过程:

- 向普通函数执行一样，形成一个私有的作用域（栈内存）
  - 形参赋值
  - 变量提升
- 【构造函数独有执行】在js代码自上而下执行之前，首先在当前形成的私有栈中创建一个对象（创建一个堆内存：暂时不存在任何的东西），并且让函数中的执行主体（**this**）指向这个新的堆内存（对象）
- 代码自上而下执行
- 【构造函数独有执行】代码执行完成后，把之前创建的堆内存地址返回（默认返回），供外面接收



```js
instanceof:检测某一个实例是否属于这个类（true/false)
function Fn(){
     ....
}
let f=new Fn()
console.log(f instanceof Fn)//true
console.log(f instanceof Array)//false
console.log(f instanceof Object)//true
```

#### 8.原型和原型链

![1573092093793](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\1573092093793.png)

**原型链：**它是一种基于___proto___向上查找的机制。当我们操作实例的某个属性或者方法的时候，首先找自己空间中私有的属性或方法，如果找到了，则结束查找，使用自己私有的即可；如果没有找到，则基于___proto___找所属类的prototype，找到就用这个公有的，若没找到则基于原型上的___proto___继续向上查找，一直找到Object.prototype的原型为止，若果依然没有，说明该属性或方法不存在.

**原型的重定向：**当我们需要给类的原型批量设置属性和方法时，一般都会让原型重定向到自己创建的对象中.

```js
function Fn(){
    ...
}
Fn.prototype={
    constructor:Fn,
    aa:function(){
        ...
    }
    ...
}
```

**阿里面试题**

```js
function Foo() {
    getName = function () {
        console.log(1);
    };
    return this;
}
Foo.getName = function BBB() {
    console.log(2);
};
Foo.prototype.getName = function AAA() {
    console.log(3);
};
var getName = function () {
    console.log(4);    //var提前声明函数，但不赋值
};
function getName() {
    console.log(5);  //function提前声明函数，同时赋值
}

Foo.getName();   //2            
getName();   //4           
Foo().getName();    //1             
getName();      //1                

new Foo.getName()    //new (Foo.getName())=>2        
new Foo().getName();   //=>(new Foo()).getName()=>Foo的实例.getName() =>3                 
```



#### 9.事件循环绑定出现的问题

```js
var btnBox=document.getElementById('btnBox'),
    inputs=btnBox.getElementsByTagName('input')
for(var i=0;i<inputs.length;i++){
    inputs[i].onclick=function(){
        alert(i)
    }
}
//绑定失败
```

**绑定失败的原因：**事件绑定是”异步编程“，当触发点击行为，绑定的方法执行的时候，外层循环已经结束；方法执行产生私有作用域，用到的变量i 不是私有的变量，按照”作用域链“的查找机制，找到的是全局下的i ，此时全局的i 已经成为循环最后一次的结果i=3。

**解决方案：**

- 方法一：闭包

> 利用闭包机制，把后期需要的索引实现存储到自己的私有作用域中，闭包有保存作用；
>
> 缺点：基于闭包解决，非常的占用内存

```js
for(var i=0;i<inputs.length;i++){
    inputs[i].onclick=(function(i){
        return function(){
            alert(i)
        }
    })(i) //=>自执行函数
}
//=>每一轮循环，都执行自执行函数形成一个私有作用域（不销毁），里面设定一个私有变量i（形参）,让i存储后期需要用到得索引,点击触发方法执行，用到变量i,向对应的上级作用域（没有销毁的那个）查找，而上级作用域中存储的i值就是我们需要的索引。
```

- 方法二：ES6

  > ES6和闭包的机制类似，ES6中使用let创建变量，会形成块级作用域，每一轮循环都会有一个自己的块级作用域，把后续需要用到的索引i 存储到自己的作用域中

  ```js
  for(let i=0;i<inputs.length;i++){
      inputs[i].onclick=function(){
          alert(i)
      }
  }
  ```

  相当于

  ```js
  {
      let i=0
      inputs[0].onclick=function(){
          alert(i)
      }    
  }
  {
      let i=1
      inputs[1].onclick=function(){
          alert(i)
      }    
  }
  ...
  //点击按钮后，执行每一个不销毁的私有作用域中的alert(i)，i不是私有的向上级作用域查找
  ```


#### 10.call,apply,bind

> 都是用来改变函数中this的指向

**call：**  [格式]：fn.call([this],[param]...)

[原理]：当call方法执行时，内部处理了一些事情：

- 首先把要操作函数fn中的this变为call方法第一个传递的实参值
- 把call方法第二个及以后的实参获取到
- 把fn函数执行，并且把第二个以后的实参传递给函数fn

**apply:**  和call基本上一样，唯一区别在于传参方式，apply传参是以数组（或类数组）方式传递给fn的

   fn.call(obj,10,20)

   fn.apply(obj,[10,20])

**bind:**  和call语法上一样，唯一区别在于立即执行还是等待执行

fn.call(obj,10,20) 改变fn中的this，并且把fn立即执行

fn.bind(obj,10,20) 改变fn中的this，fn不会立即执行

```js
document.onclick=fn.call(obj) //=>虽然this确实变为了obj，但绑定的时候就把fn执行了，点击时执行的是                                    fn返回值undefined
document.onclick=fn.bind(obj)//=>bind会把fn中的this预处理为obj,此时fn不会执行，当点击时才会把fn执                                  行
```

#### 11.Ajax

```js
//=>Ajax请求步骤
let productData=null
let xhr=new XMLHttpRequest() //创建一个请求实例
xhr.open('GET','json/product.json',false) //false同步
xhr.onreadystatechange=()=>{
      if(xhr.readyState===4 && xhr.status===200){
                productData=xhr.responseText //获取到数据
      }
}
xhr.send()
```









