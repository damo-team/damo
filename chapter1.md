# 快速上手

本章节将构建并运行一个超级简单的React应用，我们将会使用Damo-CLI来创建和管理项目。

### 1.0 先跑起来

#### →设置开发环境

在构建应用之前，我们必须先设置好开发环境，如果你的机器上还没有[**Node.js®和npm**](https://nodejs.org/en/download/)，请先安装它们。

> 请先在终端\/控制台窗口中运行命令 `node -v` 和 `npm -v`， **来验证一下你正在运行 node**`6.9.x`** 和 npm **`3.x.x`** 以上的版本。** 更老的版本可能会出现错误，更新的版本则没问题。

然后全局安装或者更新Damo CLI

```
npm install -g damo-cli@latest
```

推荐安装[vscode编辑器](https://code.visualstudio.com/)，并安装插件：`auto import`, `join-lines`, `react-beautify`, `editor config for vscode`, `damo-antd`。

#### →创建新项目

打开终端窗口，运行以下命令生成一个新项目及应用的骨架代码。

    damo new my-app
    // 如果已经手动创建好my-app目录，则终端窗口进入my-app目录，运行以下命令
    damo init
    // 你也可以通过vscode打开my-app目录，然后control + `打开编辑器中的终端窗口，运行: damo init

> 请耐心等待。 创建新项目需要花费很多时间，大多数时候都是在安装那些npm包。
>
> 创建项目成功后，会在根目录下创建应用目录结构和相关应用描述文件。

#### →启动开发服务器

进入项目根目录，并启动服务器，（启动之前先安装下node\_modules，调用`npm install`命令安装响应启动依赖到的所有node模块 ，可能需要你等待一段时间\)。

```
damo serve --open
```

使用`--open`（或`-o`）参考可以自动打开浏览器并访问 `http://localhost:8009/`（访问地址在根目录下的`config.local.json`文件可以更改）

![](/assets/屏幕快照 2017-10-13 17.00.39.png)

#### →编辑第一个React组件

Damo-CLI为我们创建了第一个React组件（根组件），你可以打开`app/scenes/index.jsx`来看。

我们打开`index.jsx`，并把title属性从`Welcome to App!!` 改为 `Welcome to My First React App!!`

```
import {Component} from 'react';
import './index.less';
export default class Root extends Component{
    static routePath = '/';
    static defaultProps = {
        title: 'My First React App!!'
    }
    render(){
        return (<div>
            <h1>Welcome to {this.props.title}</h1>
            <img src="/brand.png" />
        </div>);
    }
}
```

当你刷新浏览器，会看到修改之后的标题，可以样式改得更好看些

打开`app/scenes/index.less`，并给这个组件设置一些样式：

```
h1 { 
    color: #369; 
    font-family: Arial, Helvetica, sans-serif; 
    font-size: 250%; 
}
```

### 2.0 完成一个视图

我们已经创建了一个极其简单的应用，但是我们想让应用更贴近真实情况，把`Welcome to My First React App！!`改为 `Welcome to xxx`。xxx是接口返回的名称。

#### →定义第一个状态数据

在`app/models`目录下，创建`user.js`和`user.json`文件

编辑`user.json`，写入用户的名称

```
{
    "status": "SUCCESS",
    "data": {
        "name": "Mr Hero"
    }
}
```

#### →保存接口数据到状态数据

打开`user.js`文件，创建描述状态数据的数据模型`User`。

```
 import {BaseModel, Api, initialState, dispatch} from 'damo-core'; 

 export default class User extends BaseModel{

    @initialState profile = {};

    @dispatch getUser(){
       return this.getQuery({
           response: Api.get('http://localhost:8009/app/models/user.json'),
           proccessData: res => res.data,
           change: {
              name: 'profile',
              callback: data => data
           }
       });
    }
}
```

User模型类做了2个事情，通过`@initialState`描述了状态数据的初始化结构，`@dispatch`描述更改状态数据的方法调用。其中`@initialState`和`@dispatch`都是装饰器，装饰器是用来描述JS类以及其属性，使其按框架预设的逻辑来执行。

至于`BaseModel`类的`getQuery`方法，我们先理解为创建一个状态容器的询问语句，如果最终语句执行成功后，会变更状态容器中的指定数据。数据模型与状态管理的章节会详细介绍。

这一步以及往下看迷茫的话，可以结合概念关系图来理解下，学习基础概念参考[自述]()章节：

![](/assets/simple.png)

#### →获取状态数据

在`app/scenes`目录下创建`selector.js`，并且编辑它。

```
import {BaseSelector, Input} from 'damo-core'; 
export default class Selector extends BaseSelector{
    @Input() 
    title(state){
        return state.user.profile.name;
    }
    initialize(){
        this.getModel('user').getUser();
    }
}
```

创建一个选择器`selector`，用来从状态容器中获取指定状态数据，以便后续通过数据绑定到视图。`@Input`描述获取到的指定状态数据处理过后，最终数据绑定到视图的数据结构，这样视图中通过`this.props`可以获取绑定的数据。

当视图开始工作时，`Selector`的`initialize`钩子函数会自动执行，`initialize`中通过`BaseSelector`的`getModel`方法获取User实例，从而调用getUser方法获取用户名称，与此同时数据绑定到视图的数据结构也会重新更新，视图中this.props获取的数据是最新更新的数据，视图会重新的渲染。

#### →数据绑定

要绑定状态数据到组件，需要借助于装饰器`@View`，把`selector`和组件串通起来。

编辑`app/scens/index.jsx`

```
import React, {Component, PropTypes} from "react"; 
import ReactDOM from 'react-dom';
import {View} from 'damo-core';  
import Selector from './selector';
import './index.less';
@View({
    selector: Selector
})
export default class Root extends Component{
    static routePath = '/';
    static defaultProps = {
        title: 'My First React App!!'
    }
    render(){
        return (<div>
            <h1>Welcome to {this.props.title}</h1>
            <img src="/brand.png" />
        </div>);
    }
};
```

可以看到Root组件定义部分并没有改动，只是添加了装饰器`@View`，通过`@View`，把视图相关的元数据`selector`，注入`Selector`类来达到把状态数据绑定到组件中。

总结下出现了哪些新东西：

1. 引入`damo-core`模块

   * `BaseModel`数据模型基类，创建model需要继承BaseModel

   * `BaseSelector`,选择器基类，创建selector需要继承BaseSelector或者RxSelector（在状态与数据绑定章节详细讲解）

   * `Api`，接口调用单例。

   * 装饰器

     * `@initialState`，描述初始化状态数据
     * `@dispatch`，描述状态数据变更函数，使其函数调用时会自动更新数据到指定状态数据。
     * `@Input`，描述数据绑定到视图的数据结构
     * `@View`，描述视图需要的所有元数据，使视图按预定的程序工作。

2. 概念：组件、数据模型、选择器和数据绑定。

全部代码在一起：

```
// app/models/user.json - 模拟接口数据
/**
{
    "status": "SUCCESS",
    "data": {
        "name": "Mr Hero"
    }
}
*/

// app/models/user.js  - User数据模型，负责把数据写入到状态容器
import {BaseModel, Api, initialState, dispatch} from 'damo-core'; 

 export default class User extends BaseModel{

    @initialState profile = {};

    @dispatch getUser(){
       return this.getQuery({
           response: Api.get('http://localhost:8009/app/models/user.json'),
           proccessData: res => res.data,
           change: {
              name: 'profile',
              callback: data => data
           }
       });
    }
}

// app/scenes/selector.js - 负责从状态容器中取数据，注入到组件
import {BaseSelector, Input} from 'damo-core'; 
export default class Selector extends BaseSelector{
    @Input() 
    title(state){
        return state.user.profile.name;
    }
    initialize(){
        this.getModel('user').getUser();
    }
}

// app/scenes/index.less - 组件样式
/**
h1 { 
    color: #369; 
    font-family: Arial, Helvetica, sans-serif; 
    font-size: 250%; 
}
*/

// app/scenes/index.jsx - 组件的代码定义，数据绑定的过程通过装饰器来实现：@View({selector: Selector})

import React, {Component, PropTypes} from "react"; 
import ReactDOM from 'react-dom';
import {View} from 'damo-core';  
import Selector from './selector';
import './index.less';
@View({
    selector: Selector
})
export default class Root extends Component{
    static routePath = '/';
    static defaultProps = {
        title: 'My First React App!!'
    }
    render(){
        return (<div>
            <h1>Welcome to {this.props.title}</h1>
            <img src="/brand.png" />
        </div>);
    }
};
```

### 3.0 回顾

如你所愿，我们完成了这个“Hello, World”应用。

或许你对项目结构和文件还很陌生，甚至不清楚如何使用React，不用担心，本篇教程是希望你具备了JavaScript、React基础知识后快速实现自己的第一个项目，可以通过教程篇逐步掌握React和Damo。

#### →项目文件概览

DAMO CLI项目是做快速试验和开发企业解决方案的基础。如果你安装了`damo-cli`,可以在终端执行`damo --help`来查看`cli`的用法。

项目中有2个主要的目录，`app`和`build`目录以及2个配置文件：`config.dev.json` 和 `config.build.json`，这是damokaIFA和管理项目需要读取的配置文件。

#### →app文件夹

你的应用代码位于`app`目录

文件夹中。 所有的Damo组件、模板、样式、图片以及你的应用所需的任何东西都在那里。 这个文件夹之外的文件都是为构建应用提供支持用的。

![](/assets/app-structure.png)

| File文件 | Purpose用途 |
| :--- | :--- |
| app.{jsx, less} | 应用代码入口，把界面根容器挂到具体的页面DOM |
| scenes/index.{jsx, less} | 应用界面中的容器组件 |
| scenes/\*/index.{jsx, less} | 路由页面的容器组件。 |
| models/\*.js | 描述状态数据的数据模型类 |
| services/\*.js | 服务类 |

#### →根目录下其他文件

app目录是应用开发代码，外层的根目录还需要包含项目开发管理的描述配置文件。

![](/assets/root_structure.png)

| File文件 | Purpos用途 |
| :--- | :--- |
| config.build.json | 应用打包配置文件 |
| config.dev.json | 应用开发配置文件 |
| index.html | HTML文档文件 |
| .editorconfig | vscode编码风格设置 |
| jsconfig.json | js文件ES编码规范 |
| package.json | 项目包管理文件 |
| README.md | 项目帮助文档 |



