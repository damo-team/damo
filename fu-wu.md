# 服务

服务是完成特定一件事的JS代码，换句话说就是可复用的通用计划逻辑。

在没有抽象为服务的概念时，我们怎么去做，看下面一段代码实现

```
import React from 'react';
import ReactDOM from 'react-dom';

class User{
    getUser(){
        return fetch('https://api.github.com/users/baqian').then(response => response.json());
    }
}
const user = new User();

class Custom exends React.Component{
    constructor(){
        this.state = {};
        user.getUser().then(res => {
            this.setState({user: res});
        });
    }
    render(){
        return (<h1>{this.state.user.login}</h1>);
    }
}
```

代码中User是获取用户信息的JS类，这是一段通用逻辑，就是说在别的页面中依然可以使用该类去拿用户数据。使用User有2种方式：

1.  在组件定义的地方，初始化User。在组件内部使用user实例。
2. 在组件内部初始化User并使用user实例。

### 注册服务

### 注入服务

### 服务依赖

### Ioc控制反转

### 内置通用服务



