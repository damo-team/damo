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
    constructor(props, context){
        super(props);
        this.state = {};
        user.getUser().then(res => {
            this.setState({user: res});
        });
    }
    render(){
        return (<h1>{this.state.user.login}</h1>);
    }
}

ReactDOM.render(<Custom/>, document.body);
```

代码中User是获取用户信息的JS类，这是一段通用逻辑，就是说在别的页面中依然可以使用该类去拿用户数据。使用User有2种方式：

1. 在组件定义的地方，初始化User。在组件内部使用user实例。这种方式希望把user等多个服务维护在一个全局的对象中，方便查找。
2. 在组件内部初始化User并使用user实例，每次使用都需要初始化，消耗极小的时间成本，但如果有共享数据，仍需要存到一全局对象中。

总结起来如果通用类不需要维护共享数据，2中方式并无明显利弊，实际上这种业务场景是必然存在的，比如消息处理。

Damo把通用类统称为服务，并提供一种服务注入机制，自上下下来注入服务实例到组件内部。

不要吓到，其实很简单

```
import React from 'react';
import ReactDOM from 'react-dom';
import Damo from 'damo-core';

class User{
    getUser(){
        return fetch('https://api.github.com/users/baqian').then(response => response.json());
    }
}

class Custom exends React.Component{
    static contextTypes = {
        user: React.PropTypes.object.isRequired
    }
    constructor(props, context){
        super(props, context);

        this.state = {};
        this.context.user.getUser().then(res => {
            this.setState({user: res});
        });
    }
    render(){
        return (<h1>{this.state.user.login}</h1>);
    }
}

Damo.view(Custom, {user: User});
ReactDOM.render(<Custom/>, document.body); //=== Damo.render(Custom, document.body);
```

对比代码，有3点不同：

1. 组件内部需要声明`contextTypes`，声明需要哪个服务实例。
2. 组件`constructor`初始化时，通过`this.context.user`获取user实例。
3. `Damo.view(Custom, {user: User})`注入服务。

咋一看，比之前的代码还多了，但是带来了前所未有的方便。

1. 组件调用时，如果找不到user服务实例，会抛错，也就是可以给组件依赖的服务做异常检查，方便定位问题。
2. 服务只有都能够组件调用时才会初始化，前端称之为懒执行，减少时间开销。
3. 组件注入了user服务，子组件只要contextTypes声明，也能获取到服务实例，服务可以无限往下传递。
4. 如果User初始化依赖了其他服务，也依然会初始化并缓存到全局，即多个服务依赖了同一个服务，那么依赖的这个服务只会初始化一遍，实现共享。

### 注册服务

### 注入服务

### 服务依赖

### Ioc控制反转

### 内置通用服务



