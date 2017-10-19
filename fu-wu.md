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

1. 在组件定义的地方，初始化User。在组件内部使用user实例。这种方式希望把user等多个服务维护在一个全局的对象中，方便查找。
2. 在组件内部初始化User并使用user实例，每次使用都需要初始化，消耗极小的时间成本，但如果有共享数据，仍需要存到一全局对象中。

总结起来如果通用类不需要维护共享数据，2中方式并无明显利弊，实际上这种业务场景是必然存在的，比如消息处理。

Damo把通用类统称为服务，并提供一种服务注入机制，

### 注册服务

### 注入服务

### 服务依赖

### Ioc控制反转

### 内置通用服务



