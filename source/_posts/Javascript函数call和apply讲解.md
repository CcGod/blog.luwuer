---
title: Javascript函数call和apply讲解
date: 2018-10-26 12:41:48
tags: [js]
---

### 定义

`call()/apply()`调用一个函数, 并指定函数的this指和参数

> `A.fun.call(B, arg1, arg2)`指A对象调用B对象的fun方法，参数是`arg1, arg2`

### 区别

`call()`接收多个参数，`apply()`则只接受一个数组参数

故`A.fun.call(B, arg1, arg2)`与`A.fun.apply(B, [arg1, arg2])`是等效的

展开运算符(es6)出现之前的，我们就通过apply来把数组转化为函数的入参
```javascript
let arr = [1, 2, 3]
let add = (...values) => {
  let temp = 0
  values.forEach(value => {
    temp += value
  })
  return temp
}
add.apply(null, arr) // 由于add()中没有访问this，故null这个参数随便填什么都可以的，后面会讲到
// 等效于
add(...arr) 
```

### 举例讲解

**call()/apply()只是在更改函数的调用环境（函数中的this指向）**

##### 例1 - 函数内部不访问this

```javascript
// add this指向window对象
let add = (a, b) => a + b

// sub this指向window对象
let sub = (a, b) => a - b

add.apply(sub, [5, 2])
// 执行add(5, 2)，并且this更改为sub()中的this（也就是window/undefined），但因为add()中没有访问this.xx，所以只是单纯执行add(5, 2)~
// window/undefined视是否为严格模式
```

##### 例2 - 函数内部访问this

```javascript
let blog1 = {
  name: 'luwuer blog',
  des: function() {
    console.log(this) // blog1
    console.log(this.name) // luwuer log
  }
}

let blog2 = {
  name: 'your blog'
}

blog1.des.call(blog2) // your blog
// 执行blog1.des()，并且this被更改为blog2中的this（也就是指向blog2），所以输出blog2的name

let name = 'who care'
blog1.des.call(null) // who care
// 执行blog1.des()，并且this被更改为window，所以输出window.name
// null undefined找不到this，会被默认指向window
```

讲道这里，就能解释常常困扰初学者的表达式: 用`[].slice.call(ArrayLike)`把ArrayLike转变为数组（实质上是根据该ArrayLike生成一个有相同元素的新数组）。ArrayLike没有slice方法（就像上方例子的blog2），所以通过[]（数组实例，就像上方例子的blog1）调用slice并由call/apply更改this指向，最终slice方法返回ArrayLike元素组成的数组。

> slice方法返回一个新的数组对象，这一对象是一个由 begin 和 end（不包括end）决定的原数组的**浅拷贝**，无传参则返回当前数组的浅拷贝。

##### 例3 - 实现继承

```javascript
function Car(trademark) {
  this.wheel = 4
  this.des = () => {
    console.log(`品牌： ${trademark}`, `轮子个数： ${this.wheel}`)
  }
}

// 实现继承
function Geely() {
  this.trademark = '吉利'
  Car.call(this, this.trademark)
  // Geely()的this,替代Car()的this,只是指明了trademark
}

// 上方继承等同代码
function Geely() {
  this.trademark = '吉利'
  // Car()的代码
  this.wheel = 4
  this.des = () => {
    console.log(`品牌： ${this.trademark}`, `轮子个数： ${this.wheel}`)
  }
}

let geely = new Car('吉利')

geely.des() // 品牌： 吉利 轮子个数： 4

```