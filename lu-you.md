# 路由

前端应用中路由有2个属性，一个是url地址，叫路由路径，另外一个是路由响应后应用切换的具体页面。

在复杂应用中，路由会分多级，父级路由会存在多个子级路由，父级路由响应后，切换的页面不包含完整的内容，局部内容由子路由来填充。

多级路由的场景下，通过url路径需要识别出对应的父级路由和子级路由，父级路由是父组件，父级路由渲染成功后，紧接着子级路由渲染。我们简单理解下通过React组件如何实现。

```
class ChildRouteComponent extends React.Component{
    render(){
        return (<h1>World!</h1>);
    }
}
class ParentRouteComponent extends React.Component{
    render(){
        return (<h1>Hello {this.props.children}</h1>);
    }
}
// 假设路由响应时触发父组件调用以及嵌入子组件的逻辑。
ReactDOM.render(<ParentRouteComponent><ChildRouteComponent/></ParentRouteComponent>, document.body);
```

以上`this.props.children` 在父组件JSX模板插入内容，内容时嵌入的是子组件调用。

### 路由注册

damo.route注册路由，当应用启动时路由开始工作。

```
class ChildRouteComponent extends React.Component{
    render(){
        return (<h1>World!</h1>);
    }
}
class ParentRouteComponent extends React.Component{
    render(){
        return (<h1>Hello {this.props.children}</h1>);
    }
}

const router = damo.route('/', ParentRouteComponent);
damo.route('/child', ChildRouteComponent);
router.route('/subRoute', ChildRouteComponent);

damo.bootstrap(document.body);
```

damo中通过`damo.bootstrap(ReactElement, DOM)` 来启动应用，已经注册路由后，可以通过`damo.bootstrap(DOM` 直接启动。

路由注册的方式有2种：

1. `damo.route(path, Component)` 注册一个最上级路由，通过返回一个注册子路由的构建器。当Component存在静态属性routePath时，可以省去path参数,`damo.route(Component)`
2. 比如`router.route('/subRoute', ChildRouteComponent);`通过构建器，注册了父级路由下的子路由。另外一种父子路由注册的方式为：`damo.route(path, Component, {childRoutes: [{path, component}]});`

### 路由拦截

### 多应用路由

### 路由自动化

### 



