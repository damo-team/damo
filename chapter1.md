# 快速上手

本章节将构建并运行一个超级简单的React应用，我们将会使用Damo-CLI来创建和管理项目。

### 1.0 先跑起来

#### →设置开发环境

在构建应用之前，我们必须先设置好开发环境，如果你的机器上还没有[**Node.js®和npm**](https://nodejs.org/en/download/)，请先安装它们。

> 请先在终端\/控制台窗口中运行命令 `node -v` 和 `npm -v`， **来验证一下你正在运行 node**`6.9.x`** 和 npm **`3.x.x`** 以上的版本。** 更老的版本可能会出现错误，更新的版本则没问题。

然后全局安装Damo CLI

```
npm install @damo/cli
```

推荐安装[vscode编辑器](https://code.visualstudio.com/)，并安装插件：`auto import`, `join-lines`, `react-beautify`, `editor config for vscode`, `@damo/react-kit`。

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

进入项目根目录，并启动服务器。

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
    defaultProps = {
        title: 'My First React App!!';
    }
    render(){
        return (<div>
            <h1>Welcome to {this.props.title}</h1>
            <img src="https://baqian.gitbooks.io/damo/content/assets/brand.png" />
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

在`app/models`目录下，创建一个user的目录，并在目录下创建`user.js`和`user.json`文件

编辑`user.json`，写入用户的名称

```
{
    status: 'SUCCESS',
    data: {
        name: "Mr Hero"
    }
}
```

#### →保存接口数据到状态数据

打开`user.js`文件，创建描述状态数据的数据模型`User`。

    import {BaseModel, Api} from '@damo/core`; 
    export defualt class User extends BaseModel{ 
        @initialState properties = { 
            user: {} 
        } 
        @dispatch getUser(){ 
            return this.getQuery({ 
                request: Api.get('http://localhost:8009/app/models/user/user.json'), 
                processData: (res) => res.data, 
                change: { 
                    type: BaseModel.ASSIGN_METHODS.PURE,
                    name: 'user' 
                } 
            }); 
        } 
    }

User模型类做了2个事情，通过`@initialState`描述了状态数据的初始化结构，`@dispatch`描述更改状态数据的方法调用。其中`@initialState`和`@dispatch`都是装饰器，装饰器是用来描述JS类以及其属性，使其按框架预设的逻辑来执行。

至于`BaseModel`类的`getQuery`方法，我们先理解为创建一个状态容器的询问语句，如果最终语句执行成功后，会变更状态容器中的指定数据。数据模型与状态管理的章节会详细介绍。

这一步以及往下看迷茫的话，可以结合概念关系图来理解下，学习基础概念参考[自述]()章节：

![架构](/assets/屏幕快照 2017-10-13 16.55.25.png)

#### →获取状态数据

在`app/scenes`目录下创建`selector.js`，并且编辑它。

```
import {BaseSelector} from '@damo/core';
export default class Selector extends BaseSelector{
    @inputs props(state){
        return {
            title: state.user.name
        }
    }
    initialize(){
        this.getModel('user').getUser();
    }
}
```

创建一个选择器`selector`，用来从状态容器中获取指定状态数据，以便后续通过数据绑定到视图。`@inputs`描述获取到的指定状态数据处理过后，最终数据绑定到视图的数据结构，这样视图中通过`this.props`可以获取绑定的数据。

当视图开始工作时，`Selector`的`initialize`钩子函数会自动执行，`initialize`中通过`BaseSelector`的`getModel`方法获取User实例，从而调用getUser方法获取用户名称，与此同时数据绑定到视图的数据结构也会重新更新，视图中this.props获取的数据是最新更新的数据，视图会重新的渲染。

#### →数据绑定

要绑定状态数据到组件，需要借助于装饰器`@view`，把`selector`和组件串通起来。

编辑`app/scens/index.jsx`

```
import {Component} from 'react';
import {view} from '@damo/core';
import Selector from './selector';
import './index.less';
@view({
    selector: Selector
})
export default class Root extends Component({
    defaultProps = {
        title: 'My First React App!!'
    }
    render(){
        return (<div>
            <h1>Welcome to {this.props.title}</h1>
            <img src="https://baqian.gitbooks.io/damo/content/assets/brand.png" />
        </div>);
    }
});
```

可以看到Root组件定义部分并没有改动，只是添加了装饰器`@view`，通过`@view`，把视图相关的元数据`selector`，注入`Selector`类来达到把状态数据绑定到组件中。

总结下出现了哪些新东西：

1. 引入`@damo/core`模块

  * `BaseModel`数据模型基类，创建model需要继承BaseModel

  * `BaseSelector`,选择器基类，创建selector需要继承BaseSelector或者RxSelector（在状态与数据绑定章节详细讲解）

  * `Api`，接口调用单例。

  * 装饰器

    * `@initialState`，描述初始化状态数据
    * `@dispatch`，描述状态数据变更函数，使其函数调用时会自动更新数据到指定状态数据。
    * `@inputs`，描述数据绑定到视图的数据结构
    * `@views`，描述视图需要的所有元数据，使视图按预定的程序工作。

2. 概念：组件、数据模型、选择器和数据绑定。

### 3.0 回顾

#### →项目文件概览

#### →src文件夹

#### →根目录下其他文件

