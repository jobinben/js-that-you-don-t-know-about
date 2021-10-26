# this

- **this**实际上是在函数`被调用时`发生的绑定，它指向什么完全取决于函数`在哪里被调用`。（类似于 `动态作用域`）

## 关于 this 的误解

this即`不指向函数自身`也`不指向函数的词法作用域`。

# 绑定规则 - 4条

## 1. 默认绑定

- 在**非严格模式**下，`this`默认绑定在全局变量，
  在浏览器上的话，`this`就是指向`window`
- **严格模式**下，`this`的绑定默认是`undefined`

注意: 对于`默认绑定`来说，决定this绑定对象的并不是调用位置是否处于严格模式，而是**函数体是否处于严格模式**。

## 2. 隐式绑定

如: 
```js
function foo() {
    console.log(this.a)
}
let obj = {
    a: 2,
    foo: foo
}

obj.foo() // 输出 2  看看看！！ 隐式绑定
```

`obj.foo()`这里就是隐式把this绑定在foo调用时的上下文上的obj的作用域。

缺点: 可能会丢失this的绑定


## 3. 显示绑定

通过`bind()`方法，或者通过`call()`和`apply()`方法指定`this`的绑定。

## 4. new 绑定

如:
```js
function foo(a) {
    this.a = a
}
let bar = new foo(2)
console.log(bar.a) // 2
```
上面的this转换过程为如下:
```js
this => {
    a: 2
}
bar = this => {
    a: 2
}

bar.a // 2

```

**使用new来调用foo(...)时，我们会构造一个`新对象`并把它`绑定到foo(...)调用中的this`上。**


# this的优先级

**new绑定 -> 显示绑定 -> 隐式绑定 -> 默认绑定**

# 判断this

按照下面的**顺序**来进行判断。

1. 函数是否在new中调用(new 绑定)？ 如果是的话this绑定的是`新创建的对象`。
   ```js
   let bar = new foo()
   ```
2. 函数是否通过bind、call、apply(显示绑定)或者硬绑定调用? 如果是的话，this绑定的是`指定的对象`。
   ```js
   let obj = {}
   let bar = foo.call(obj)
   ```
3. 函数是否在某个上下文对象中调用(隐式绑定)? 如果是的话，this绑定的是那个`上下文对象`。
   ```js
    let obj = {
        foo: function(){}
    }
    let bar = obj.foo()
   ```
4. 如果以上都是不的话，使用默认绑定。如果在严格模式下，就绑定到`undefined`，否则绑定到`全局对象`（浏览器的话是window）。
   ```js
   let bar = foo()
   ```
   
# ES6中的箭头函数的this

ES6中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域决定this。具体来说，箭头函数会继承外层函数调用的this绑定(无论this绑定到什么)

```js
function foo() {
    // 返回一个箭头函数
    return (a) => {
        //this继承来自foo()
        console.log(this.a)
    }
}

let obj1 = {
    a: 'obj1'
}
let obj2 = {
    a: 'obj2'
}
let bar = foo.call(obj1) // foo函数已经绑定了obj1
bar.call(obj2); // 输出'obj1', 不是 'obj2' ！！
```
**箭头函数的绑定无法被修改！！（new也不行！）**
因为箭头函数本身就没有this。


   # 小结

   **如果要判断一个运行中的函数的this绑定，就需要找到这个函数的直接调用位置。找到之后就可以`顺序`应用this的四条绑定规则。**

   1. new 调用？**绑定到新创建的对象。**
   2. call/apply/bind 调用？**绑定到指定的对象。**
   3. 上下文对象调用?   **绑定到那个上下文对象。**
   4. 默认：**函数体严格模式下绑定到undefined，否则绑定到全局对象。**

