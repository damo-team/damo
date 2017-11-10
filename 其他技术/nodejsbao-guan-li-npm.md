# NodeJS包管理与分发工具NPM

NPM的全称是Node Package Manager，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准。

简单来讲，NPM就像是NodeJS世界里的软件管家，npm工具会根据你声明的依赖，通过一句`npm install`就都给您安装好。

### CNPM

然而因为种种你知道或者不知道的原因，npm无法正常的下载依赖包，这时推荐使用CNPM来替代NPM。你应该已经猜到了，CNPM就是国内和谐版的NPM，由taobao为您提供服务。cnpm的使用几乎和npm完全一致。

### Node.js与npm以及cnpm的安装

如果你的机器上还没有[**Node.js®和npm**](https://nodejs.org/en/download/)，请先下载安装它们。安装后在命令行中输入`npm -v`查看npm版本，推荐使用最新版本。确认npm安装成功后，全局安装cnpm工具

```
npm config set registry https://registry.npm.taobao.org
npm install -g cnpm
```







