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



### 路由自动加载

### 路由拦截

### 多应用路由

### 



