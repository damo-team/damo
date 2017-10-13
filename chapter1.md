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

打开app\/scenes\/index.less，并给这个组件设置一些样式：

```
h1 { 
    color: #369; 
    font-family: Arial, Helvetica, sans-serif; 
    font-size: 250%; 
}
```

### 2.0 完成一个视图

我们已经创建了一个极其简单的应用，但是我们想让应用更贴近真实情况，把`Welcome to My First React App!`改为 `Welcome to xxx`。xxx是接口返回的名称。

#### →定义第一个状态数据

在app\/models目录下，创建一个user的目录，并在目录下创建user.js和user.json文件

编辑user.json，写入用户的名称

```
{
    status: 'SUCCESS',
    data: {
        name: "Mr Hero"
    }
}
```

#### →保存接口数据到状态数据

打开user.js文件，创建描述状态数据的数据模型User。

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

#### →获取状态数据

在app\/scenes目录下创建selector.js，并且编辑它

```
import {BaseSelector} from '@damo/core';
export default class RootSelector extends BaseSelector{
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

#### →数据绑定

要绑定状态数据到组件，需要借助于装饰器，把selector和组件串通起来。

编辑app\/scens\/index.jsx

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

可以看到Root组件定义部分并没有改动，只是添加了装饰器@view，通过@view，把视图相关的元数据selector，注入Selector类来达到把状态数据绑定到组件中。

### 3.0 回顾

#### →项目文件概览

#### →src文件夹

#### →根目录下其他文件

