# 数据绑定

数据绑定是指和store容器建立连接后，从store中获取响应数据注入到组件中，这个过程由damo托管自动化进行，而我们需要配置好哪个组件需要注入哪些数据。

```
class UserSelector extends damo.BaseSelector{
    static dataBindings = () => {
        return {
            profile: damo.getModel('user').state.profile
        }
    }

    static eventBindings = () => {
        return {
            getUser: this.getUser.bind(this)
        }
    }

    initialize(ownProps){
        // this.getUser();
    }

    getUser(){
        this.getModel('user').getUser();
    }
}

class Custom extends React.Component{
    componentWillMount(){
        this.props.getUser();
    }
    render(){
        return (<h1>{this.props.profile.login}</h1>);
    }
}
damo.init();
const ViewComponent = damo.view(UserSelector, Custom);
damo.bootstrap(ViewComponent, document.body);
```

例子中创建了UserSelector，并往组件中注入数据结构`profile`和数据行为`getUser`，在组件Custom通过this.props来访问注入的数据和行为。

在Custom的`componentWillMount`时调用`getUser`来更新store数据，数据一旦更新成功damo会自动触发注入新更新好的数据profile。从而实现组件重新渲染。整个过程我们总结一下。

1. selecotr必须继承于`BaseSelector`基类
   1. `dataBindings`静态属性，向组件注入数据结构。
   2. `eventBindings`静态属性，向组件注入行为方法。
   3. 注入的数据，在组件内部通过`this.props`可以访问到。
   4. 数据模型添加是在全局中，`selector`通过`this.getModel`（由BaseSelector继承而来）可以访问model实例，从而进行操作，也可以通过`damo.getModel` 获取到实例。
   5. selector存在一个钩子函数，`initialize`是指组件`compoentWillMount`时触发，起到相同的效果。
2. 组件实际上不用太关心`selector`，对于组件来说，需要的状态数据都是通过`this.props`获取到，保持了组件的纯净，脱离了damo扔可以使用，提高复用性。
3. `damo.view(UserSelector, Custom)`建立`selector`到`component`的数据绑定机制，返回新的组件来使用。

> damo.view\(\[selector\], component, \[providers\]\)函数的标准调用，selector可以为空，providers是需要注入的服务，在服务章节有讲解。

selector同时一种特殊的服务，从前面我们了解到，在组件中通过contextTypes声明注入的服务，通过this.context可以访问到服务实例，所以通过this.context.selector可以访问到selector实例，改造一下Custom组件。

```
class Custom extends React.Component{
    static contextTypes = {
        selector: React.PropTypes.object.isRequired
    }
    componentWillMount(){
        this.context.selector.getUser();
    }
    render(){
        return (<h1>{this.props.profile.login}</h1>);
    }
}
```

### 回顾damo的数据驱动机制![](/assets/import.png)

数据模型model，向store写入数据，通过damo.model添加数据模型。

选择器selector，从store读取数据，通过damo.view绑定选择器到组件。

### 服务自动化

```
damo.autoLoadServices(require.context('./services', false, /\.js$/))
```

现在是否对damo有个清晰的认识了，赶紧去试下吧。

