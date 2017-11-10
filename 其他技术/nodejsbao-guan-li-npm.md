# NodeJS包管理与分发工具NPM

NPM的全称是Node Package Manager，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准。

简单来讲，NPM就像是NodeJS世界里的软件管家，npm工具会根据你声明的依赖，通过一句`npm install`就都给您安装好。

### CNPM

然而因为种种你知道或者不知道的原因，npm无法正常的下载依赖包，这时推荐使用CNPM来替代NPM。你应该已经猜到了，CNPM就是国内和谐版的NPM，由taobao为您提供服务。cnpm的使用几乎和npm完全一致。

### Node.js与npm以及cnpm的安装

如果你的机器上还没有[**Node.js®和npm**](https://nodejs.org/en/download/)，请先安装它们。

```
npm install -g damo-cli@latest
```

安装最新版CLI工具，按装好后，直接输入damo即会提示使用帮助。

```py
Usage: damo [command] [options] [arguments]

where <command> is one of:
        new, init, serve, build, dll

  -o, --open         argument: [Option] [Config Path] | watch files for changes and run for develop
  -H, --hot          argument: [Option] run in develop by hot-module-replace
  -a, --assets       argument: [Option] [Config Path] | run command without entry html
  -h, --help         show cli usage
  -v, --version      show cli version
```

### 命令说明

1. `damo new 项目名` 新建一个damo应用，并初始化好脚手架，一般来说你还需要手动npm install完成依赖模块的安装。
2. `damo init` 如果你已经建立好项目目录，那么可以在项目根目录下运行改命令生成脚手架目录结构。
3. `damo serve --open --hot config.dev.json` 启动本地开发环境，open和hot参数分别是打开浏览器以及热模块更新HMR，参数都有简写，比如`-o` 和`-H` ，config.dev.json是默认启动以来的配置文件，你可以指定新的配置文件，不填为默认的。
4. `damo build --assets config.build.json` 打包前端目录， assets参数是指不编译index.html（简写`-a`，指需要打包静态JS\CSS，config.build.json是打包默认依赖的配置文件，可以填写新的配置文件，不填为模块的。
5. `damo --help` 查看帮助文件。help参数简写`-h` 
6. `damo --version` 查看CLI的版本, version参数简写`-v` 



