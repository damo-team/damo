# Node.js包管理与分发工具npm

NPM的全称是Node Package Manager，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准。

简单来讲，NPM就像是NodeJS世界里的软件管家，npm工具会根据你声明的依赖，通过一句`npm install`就都给您安装好。

### CNPM

一般情况下，在公司内部的大型项目中，都会自己搭建私有的npm服务器，而cnpm提供了很好的私有npm服务器解决方案。

然而因为种种你知道或者不知道的原因，npm有时无法正常的下载依赖包，这时推荐使用CNPM来替代NPM。你应该已经猜到了，CNPM提供了国内镜像版的NPM，由taobao为您提供服务。cnpm的使用几乎和npm完全一致。

### Node.js与npm以及cnpm的安装

如果你的机器上还没有[**Node.js®和npm**](https://nodejs.org/en/download/)，请先下载安装它们。安装后在命令行中输入`npm -v`查看npm版本，推荐使用最新版本。确认npm安装成功后，全局安装cnpm工具，如果是在内网安装，请使用公司提供的npm镜像地址代替taobao镜像地址

```
npm config set registry https://registry.npm.taobao.org
npm install -g cnpm
```

安装成功后，就可以使用cnpm命令来替代npm了。我们可以使用以下命令指定私有npm服务器地址

```
cnpm config set registry http://99.12.98.12:7001
cnpm config set disturl http://99.12.98.12:7001/mirrors/node
```

### 环境变量设置

如果您使用的是windows，那么您还需要检查系统环境变量，并在环境变量Path中加入`C:\Program Files\nodejs\node_modules\`

### CNPM私有库的使用

新建模块目录service-test，进入service-test目录下新建文件a.js

```
function hello(name){
    console.log('hello '+name);
}
export.hello = hello;
```

再新建文件b.js

```
var h = require('./a');
h.hello('Giraffee');
```

在命令行下，进入service-test目录，输入`cnpm init `，之后依次输入项目名称，版本号等信息。注意项目名称要使用私有库指定的前缀：例如@scope-name/module-name，否则无法上传至私有库。在初始化项目后，会在目录下生成package.json。在项目目录下新建文件README.md，对项目进行解释说明，该内容会展示在cnpm的项目页中。

使用cnpm账号登录命令`cnpm login`，输入由管理员提供的用户名和密码即可登录，首次登录时需要设置密码。登录后可以使用`cnpm whoami`，查询当前登录用户。

使用`cnpm publish`命令，上传模块到cnpm私有库。

