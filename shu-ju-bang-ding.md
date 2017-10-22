# 数据绑定

数据绑定是指和store容器建立连接后，从store中获取响应数据注入到组件中，这个过程由damo托管自动化进行，而我们需要配置好哪个组件需要注入哪些数据。

```
class UserSelector extends damo.BaseSelector{
    static dataBindings = {
        profile: damo.toselect('user', 'profile')
    }

    static eventBindings = {
        getUser: (dispatch, ownProps){
            this.getModel('user').getUser();
        }
    }

    initialize(ownProps){
        // this.getModel('user').getUser();
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

例子中创建了UserSelector，并往组件中注入数据结构profile和数据行为getUser，在组件Custome通过this.props来访问注入的数据和行为。

在Custom的componentWillMount时调用getUser来更新store数据，数据一旦更新成功damo会自动触发注入新更新好的数据profile。从而实现组件重新渲染。整个过程我们总结一下。

1. selecotr必须继承于BaseSelector基类
   1. dataBindings静态属性，向组件注入数据结构，通过damo.toselect\(modelName, dataKey\) 来声明即将注入哪个数据模型下的数据。
   2. eventBindings静态属性，向组件注入行为方法，其内部是通过model实例的行为来发生改变。
   3. 注入的数据，在组件内部通过this.props可以访问到。
   4. 数据模型添加是在全局中，selector通过this.getModel（由BaseSelector继承而来）可以访问model实例，从而进行操作。
   5. selector存在一个钩子函数，initialize是指组件compoentWillMount时触发，起到相同的效果。
2. 组件实际上不用太关心selector，对于组件来说，需要的状态数据都是通过this.props获取到，保持了组件的纯净。



