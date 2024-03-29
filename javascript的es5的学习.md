# javascript的es5语法的新增的特性的学习

## 新增的严格模式
1. 开启方法
    - 为全局开启
    - 为函数开启
2. 严格模式中的变化

- 会将过失错误转成异常
    + 严格模式下无法再意外的创建全局变量
    + 严格模式会引起静默失败的赋值操作抛出异常。
    + 严格模式下，视图删除不可删除的属性时会抛出异常（之前这种操作不会产生任何的效果）
    + 严格模式要求对象内的属性名在对象内必须唯一。正常模式下重名属性是允许的，最后一个重名的属性决定其属性的值。
    + 严格模式要求函数的参数名唯一。在正常模式下，最后一个重名的参数会覆盖之前的重名的参数。（之前的参数任然可以通过arguments[i]来访问，还不是完全的访问。。。）
    + 严格模式禁止八进制的语法
    + es的严格模式禁止设置primitive值得属性
- 简化变量的使用
    + 严格模式禁用with
    +   *严格模式下的eval不在为上层范围引入新的变量*在正常模式新爱，代码eval("var x;")会给上层函数或者全局引入一个新的变量x。
    * 如果函数eval被在严格模式下的eval(...)以表达式的形式调用是。**其代码会被当做严格模式下的代码执行**
    + 严格模式禁止删除声明变量。delete name 在严格模式下会引起语法错误
- 让eval和arguments变的简单
    + 第一，名称eval和arguments不能通过程序语法被绑定或赋值。
    + 第二，严格模式下，参数的值不会随着arguments对象的值得变化的改变而变化。
        * 正常模式下对于第一个参数是arg的函数，对arg赋值会同时赋值给arguments[0]，反之亦然（除非没有参数，或者arguments[0]被删除）
    + 第三，不再支持arguments.callee。
- 安全的javascript
    + 第一，在严格模式下通过this传递给一个函数的值不会强制转换为一个对象。
    + 第二，在严格模式中再也不能通过广泛实现的ECMAScript扩展“游走”于javascript的栈中
    * 也就是如果一个fun（函数）在严格模式下，那么fun.callr和fun.arguments都是不可删除的的属性而且在存值和取值的时候都会报错
    + 第三，严格模式下arguments不会再提供访问于调用这个函数相关的变量的途径。
- 为未来的ECMAScript版本铺平道路
    + 第一，在严格模式中一部分字符比变成了保留的关键字。
    + 严格模式禁止了不在脚本或者函数上的函数声明。