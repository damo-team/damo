# 编程风格指南 {#-}

下面的内容主要参考了[Airbnb](https://github.com/airbnb/javascript)公司的 JavaScript 风格规范。



## 1.块级作用域

**（1）let 取代 var**

ES6 提出了两个新的声明变量的命令：`let`和`const`。其中，`let`完全可以取代`var`，因为两者语义相同，而且`let`没有副作用。

`var`命令存在变量提升效用，`let`命令没有这个问题。

