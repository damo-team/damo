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
        this.context.getUser();
    }
    render(){
        return (<h1>{this.props.profile.login}</h1>);
    }
}
damo.init();
const ViewComponent = damo.view(UserSelector, Custom);
damo.bootstrap(ViewComponent, document.body);
```





