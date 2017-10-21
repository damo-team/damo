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
import damo from 'damo-core';

class User {
  getUser() {
    return fetch('https://api.github.com/users/baqian').then(response => response.json());
  }
}

class Custom extends React.Component {
  static contextTypes = {
    user: React.PropTypes.object.isRequired
  }
  constructor(props, context) {
    super(props, context);

    this.state = {
      user: {}
    };
    this
      .context
      .user
      .getUser()
      .then(res => {
        this.setState({user: res});
      });
  }
  render() {
    return (
      <h1>{this.state.user.login}</h1>
    );
  }
}
damo.init();
const viewComponent = damo.view(Custom, {user: User});

damo.bootstrap(viewComponent, document.body);
```

对比代码，有3点不同：

1. 组件内部需要声明`contextTypes`，声明需要哪个服务实例。
2. 组件`constructor`初始化时，通过`this.context.user`获取user实例。
3. `Damo.view(Custom, {user: User})`分配服务，返回封装好的组件（HOC）。
4. `damo.bootstrap` 渲染组件并挂载到body下。

咋一看，比之前的代码还多了，但是带来了前所未有的方便。

1. 组件调用时，如果找不到user服务实例，会抛错，也就是可以给组件依赖的服务做异常检查，方便定位问题。
2. 服务只有都能够组件调用时才会初始化，前端称之为懒执行，减少时间开销。
3. 组件注入了user服务，子组件只要contextTypes声明，也能获取到服务实例，服务可以无限往下传递。
4. 如果User初始化依赖了其他服务，也依然会初始化并缓存到全局，即多个服务依赖了同一个服务，那么依赖的这个服务只会初始化一遍，实现共享。

```
function A(){
}

class B {
    static contextTypes = {
        a: PropTypes.object.isRequired
    }
}
damo.view(Custom, {
    user: User,
    a: A,
    b: B
});
```

这里给Custom分配了3个服务，其中A只会初始化一次，即B中依赖的A服务，拿到是A初始化好的实例。服务中依赖其他服务，也通过contextTypes来声明.

### 注册服务

注册服务是预先注册好服务类，服务可以注册到全局，也可以在局部组件内。在框架启动时或者组件调用时，服务才会被初始化（懒执行），从而提高页面渲染速度。

```
// 创建方式1： 函数式服务
function A(){
    return true;
}
A.contextTypes = {}
// 创建方式2： 数组式服务
const B = ['a', function (a){
    return {bool: a};
}]
// 创建方式3： class服务
class C{
    static contextTypes = {
        a: React.Proptypes.boolean.isRequired,
        b: React.Proptypes.object.isRequired,
    }
    constructor(){
    }
}

// 注册服务1：全局服务，displayName指定服务实例名称
A.displayName = 'a';
damo.service(A)
// 注册服务2：全局服务，map的key指定服务名称
damo.service({b: B});
// 注册服务3：全局服务，一次注册多个
C.displayName = 'c';
damo.service([C]);

// 注册服务4：局部服务，挂在组件下
class Com extends React.Component{
    static contextTypes = {
        b: React.Proptypes.object.isRequired,
        c: React.Proptypes.object.isRequired
    }
}
// 调用组件要注入服务，需要调用ComByServices组件，而不是Com组件。
const ComByServices = damo.view(Com, {c: C});
```

服务的操作比较灵活，代码中展示了服务的注册和服务的注入。

1. 3种服务创建方式
   1. 数组形式，需要显示声明依赖的服务实例名称，在数组最后一项是服务的函数定义，在服务调用时参数中会传入依赖的服务实例引用。
   2. 其他方式，需要通过静态属性contextTypes声明依赖的服务实例名称。
2. 4种服务注册
   1. map结构注册服务时，key为服务实例的名称。
   2. 其他方式注册服务，需要通过服务的静态属性displayName来声明服务的实例名称。
   3. 注册服务可以以数组形式注册多个。

### 服务依赖和注入

服务依赖是指，当前服务被初始化时，服务内依赖的外部服务同时会被初始化并注入其服务实例，服务注入有2个时机。

1. 注册的全局服务会立即被调用，并把服务实例存储全局作用域。
2. 注册的局部服务，只能指定挂在具体组件下，其组件及子组件在调用时，框架会根据组件依赖的服务，自动注入服务的实例引用。
   1. 需要注意的是，子组件依赖已经存在的服务实例，也是通过静态属性contextTypes声明。

> damo中的服务注入机制实际上是兼容了angular的DI（依赖注入）和react的Ioc（控制反转）
>
> DI的好处是由框架的配置当前依赖的服务实例，比如全局服务的注入。
>
> Ioc的好处是在注入服务时会去检查服务依赖的异常状态，从父组件来决定是否给以下所有子组件分配服务。

### 内置服务

damo会提供有利于构建复杂应用的通用服务。

事实上BaseSelector就是一种特殊的服务，selector类都需要继承于BaseSelector，用于链接store容器，从状态容器获取数据绑定到组件中。在[数据绑定](/状态与数据绑定.md)篇章详细介绍。

更多内置服务敬请期待

