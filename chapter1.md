# 快速上手

本章节将构建并运行一个超级简单的React应用，我们将会使用Damo-CLI来创建和管理项目。

如果你很心急，直达[mini-app项目](#minapp)

### 1.0 先跑起来

#### →设置开发环境

在构建应用之前，我们必须先设置好开发环境，如果你的机器上还没有[**Node.js®和npm**](https://nodejs.org/en/download/)，请先安装它们。关于npm更多信息可以参考Node.js包管理npm章节

> 请先在终端\/控制台窗口中运行命令 `node -v` 和 `npm -v`， **来验证一下你正在运行 node**`6.9.x`** 和 npm **`3.x.x`** 以上的版本。** 更老的版本可能会出现错误，更新的版本则没问题。

然后全局安装或者更新[Damo CLI ](/其他技术/damo-cli.md)

```
npm install -g damo-cli@latest
```

推荐安装[vscode编辑器](https://code.visualstudio.com/)，并安装插件：`auto import`, `join-lines`, `react-beautify`, `editor config for vscode`, `damo-antd`。

> Damo CLI帮助你更方便创建一个damo应用。

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

![](/assets/屏幕快照 2017-10-22 上午10.49.17.png)

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
 import damo from 'damo-core'; 

 export default class User extends damo.BaseModel{
    static displayName = 'user'

    static initialState = {
       profile: {}
    }

    getUser(){
       return this.setState({
           profile: {
              response: damo.Api.get('http://localhost:8009/app/models/user.json'),
              proccessData: res => res.data
           }
       });
    }
}
```

数据模型是一类状态数据的集合，数据模型做2个事情，描述状态数据的初始化结构 以及 定义改变状态数据的行为方法。`this.setState` 更新状态数据。

静态属性`initialState` 描述了状态数据的初始化结构，这里值User下有一个状态数据，名为`profile` ; `displayName` 声明这个数据模型初始化后，存到全局的实例名称，比如你可以通过`damo.getModel('user')` 获取到这个User的实例。（那什么时候初始化呢，在调用`damo.model(User)` 时被初始化的，数据模型章节会详细介绍）。

> 同ReactComponent中的this.setState不同（定义一个真实数据），BaseModel中的this.setState定义的是获取真实数据的配置，以上代码表示通过一个接口的promise获取到数据，并更新profile状态数据。

#### →获取状态数据

在`app/scenes`目录下创建`selector.js`，并且编辑它。

```
import damo from 'damo-core'; 
export default class Selector extends damo.BaseSelector{
    static dataBindings = ['user'];
    static eventBindings = ['user'];
}
```

创建一个选择器`selector`，用来从状态容器中获取指定状态数据，以便后续通过数据绑定到视图。

静态属性`dataBindings` 声明要注入哪个数据模型的状态数据，`eventBindings`声明了要注入哪个数据模型的行为方法。

> 到目前为止这个Selector类还只是一个定义，如果不和组件绑定起来，起不到任何作用。和组件绑定后生成一个视图组件（通过`damo.view(selector, component)`），这样Selector从数据模型注入的状态数据和行为方法会传递给组件，最终组件内部通过this.props获取访问到。

#### →数据绑定

要绑定状态数据到组件，需要借助于装饰器`@View`，把`selector`和组件串通起来。

编辑`app/scens/index.jsx`

```
import React, {Component, PropTypes} from "react"; 
import ReactDOM from 'react-dom';
import damo from 'damo-core';  
import Selector from './selector';
import './index.less';

class Root extends Component{
    static defaultProps = {
        profile: {
            login: 'My First React App!!'
        }
    }
    componentWillMount(){
        // 因为damo.view绑定了Selector和组件，可以通过this.props.getUser访问从User注入的行为方法
        this.props.getUser();
    }
    render(){
        // 因为damo.view绑定了Selector和组件，可以通过this.props.profile访问从User注入的状态数据
        return (<div>
            <h1>Welcome to {this.props.profile.login}</h1>
            <img src="/brand.png" />
        </div>);
    }
};
// 绑定Selector和组件，生成视图
export default damo.view(Selector, Root);
```

我们回顾一下以上内容，可以结合概念关系图来理解下，学习基础概念参考[自述]()章节：

![](/assets/simple.png)

概览图解释为：

1. 前端应用创建时，同时会创建共享的单例对象，叫状态容器store。应用中几乎所有的状态数据都存在store。
2. 数据模型model表示一类状态数据的集合，并且描述了这些数据状态的初始化结构，以及改变这些数据的方法。只有数据模型可以往store写入数据。
3. 选择器selector相当于是组件的控制器，因为只有和组件绑定在一起才有意义。selector主要是从指定的数据模型实例中把状态数据和数据行为注入到组件，组件通过this.props可以访问到。

把以上代码全部集中在一起再来理解下：

### 迷你App {#minapp}

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

import damo from 'damo-core'; 
import React, {Component, PropTypes} from "react"; 
import ReactDOM from 'react-dom';

// User数据模型 - 负责把数据写入到状态容器
class User extends damo.BaseModel{
    static displayName = 'user'

    static initialState = {
       profile: {}
    }

    getUser(){
       return this.setState({
           profile: {
              response: damo.Api.get('http://localhost:8009/app/models/user.json'),
              proccessData: res => res.data
           }
       });
    }
}

// selector类 - 负责从状态容器中取数据，注入到组件
class Selector extends BaseSelector{
    static dataBindings = ['user'];
    static eventBindings = ['user'];

}


// 组件的代码定义 - 数据绑定的过程通过damo.view
class Root extends Component{
    static defaultProps = {
        profile: {
            login: 'My First React App!!'
        }
    }
    componentWillMount(){
        // 因为damo.view绑定了Selector和组件，可以通过this.props.getUser访问从User注入的行为方法
        this.props.getUser();
    }
    render(){
        // 因为damo.view绑定了Selector和组件，可以通过this.props.profile访问从User注入的状态数据
        return (<div>
            <h1>Welcome to {this.props.profile.login}</h1>
            <img src="/brand.png" />
        </div>);
    }
};

// 初始化
damo.init();
// 注册数据模型
damo.model(User);
// 生成视图组件
const viewComponent = damo.view(Selector, Root);
// 运行
ReactDOM.render(viewComponent, document.body);
```

> 为了对应用代码有个概览认识，下面代码展示了一个小应用实现, 整个拷贝到app/app.jsx覆盖掉原有代码，想要深入理解每个部分可以学习教程章节。

### 3.0 回顾 {#minapp}

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



