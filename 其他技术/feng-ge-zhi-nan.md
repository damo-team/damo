# 编程风格指南 {#-}

下面的内容主要参考了[Airbnb](https://github.com/airbnb/javascript)公司的 JavaScript 风格规范。



## 1.块级作用域

**（1）let 取代 var**

ES6 提出了两个新的声明变量的命令：`let`和`const`。其中，`let`完全可以取代`var`，因为两者语义相同，而且`let`没有副作用。

```
'use strict';

if (true) {
  let x = 'hello';
}

for (let i = 0; i < 10; i++) {
  console.log(i);
}
```

上面代码如果用`var`替代`let`，实际上就声明了两个全局变量，这显然不是本意。变量应该只在其声明的代码块内有效，`var`命令做不到这一点。

`var`命令存在变量提升效用，`let`命令没有这个问题。

