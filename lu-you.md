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

// 主要注册过了服务，启动只需要指定从哪个DOM为入口即可。
damo.bootstrap(document.body);
```

damo中通过`damo.bootstrap(ReactElement, DOM)` 来启动应用，已经注册路由后，可以通过`damo.bootstrap(DOM)` 直接启动。

路由注册的方式有2种：

1. `damo.route(path, Component)` 注册一个最上级路由，通过返回一个注册子路由的构建器。当Component存在静态属性routePath时，可以省去path参数,`damo.route(Component)`
2. 比如`router.route('/subRoute', ChildRouteComponent);`通过构建器，注册了父级路由下的子路由。另外一种父子路由注册的方式为：`damo.route(path, Component, {childRoutes: [{path, component}]});`

路由配置的标准语法为`damo.route(path, Component, option)`

### 路由拦截

路由拦截有3种情景，路由切换后组件渲染时，路由切换前组件销毁前 和 路由离开（浏览器关闭时）。

```
damo.route(path, Component, {
    onEnter(){
    },
    onLeave(){
    },
    onDestroy(){
    }
})
```

1. onEnter是切换后组件渲染时触发
2. onLeave是切换后组件销毁时触发
3. onDestroy是路由销毁时触发，父级路由配置好后，子级路由销毁时也同样会触发。

### 多应用路由

这是一种特殊场景，应用中有多个大型业务，每个业务是一个子应用，每个子应用有完成的路由配置。

按配置路由的角度来分析，就是根路由`/` 指向默认的子应用，切换路由时则是切换子应用。需要通过`damo.bootstrap`来实现

```
const router = damo.route('/', Root, {indexRoute: subApp1});
router.route('/sub1', subApp1, {childRoutes: [...]});
router.route('/sub2', subApp2, {childRoutes: [...]});
damo.bootstrap(document.body);
```

更复杂一点，我们还需要在应用加上父级业务，比如访问`/demo` 才会进入到Root组件，其中indexRoute是做默认路由重定向到指定组件。

```
damo.bootstrap(document.body, 'demo'); //跟设定的路由加上新的根路径
```

我们还可以通过damo.bootstrap一次配置好所有路由

```
damo.bootstrap([{
    path: '/',
    component: Root,
    indexRoute: subApp1
    childRoutes: [
        {
            path: '/sub1',
            component: subApp1,
            childRoutes: [...]
        },
        {
            path: '/sub2',
            component: subApp2,
            childRoutes: [...]
        }
    ]
}], document.body, 'demo');
```

### 路由自动化

路由自动化是结合app目录，通过自动遍历目录结构生成App的路由

```
damo.autoLoadRoutes(require.context('./scenes', true, /index\.jsx$/));
```

以上代码表示遍历`./**/index.jsx` 所有文件，并注册好路由，目录的层级结构决定了父子路由的关系。

### 



