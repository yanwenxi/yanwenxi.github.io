---
title: YARN, 一个可能取代npm的javascript包管理
date: 2017-01-12 10:55:40
tags: yarn npm
---
Facebook刚刚推出了一款名叫Yarn的开源JavaScript包管理器，承诺比各大流行npm包的安装更可靠，且速度更快。根据你所选的工作包的不同，该公司称Yarn可以将安装时间从数分钟减少至几秒钟.

它的目的是解决这些团队使用 npm 面临的少数问题，即：
1.安装的时候无法保证速度/一致性
2.安全问题，因为 npm 安装时允许运行代码

#### yarn.lock 文件
npm 和 Yarn 都使用 package.json 来跟踪项目的依赖，版本号并非一直准确，因为你可以定义版本号范围，这样你可以选择一个主版本和次要版本的包，但让 npm 安装最新的补丁也许可以修改一些 bug。

理想状态下使用语义化版本发布补丁不会包含大的变化，但不幸的是这必非真理。npm 的这种策略可能导致两台拥有相同 package.json 文件的机子安装了不同版本的包，这可能导致一些错误。

为了避免包版本的错误匹配，一个确定的安装版本被固定在一个锁文件中。每次模块被添加时，Yarn 就会创建（或更新）yarn.lock 文件，这样你就可以保证其它机子也安装相同版本的包，同时包含了 package.json 中定义的一系列允许的版本。

在 npm 中同样可以使用 npm shrinkwrap 命令来生成一个锁文件，这样在使用 npm install 时会在读取 package.json 前先读取这个文件，就像 Yarn 会先读取yarn.lock 一样。这里的区别是 Yarn 总会自动更新 yarn.lock，而 npm 需要你重新操作。

yarn管理器有一个很重要的文件需要注意，就是yarn.lock，这个是用来依赖的正确性，快速可靠安装的；是执行cli的时候自动生成的，在项目的根目录下，需要保留！！！！不要编辑它，这是自动生成的.在其他电脑初始化，必须记得把package.json和yarn.lock复制过去，简直就是秒下载【缓存机制】.

#### 并行安装
每当 npm 或 Yarn 需要安装一个包时，它会进行一系列的任务。在 npm 中这些任务是按包的顺序一个个执行，这意味着必须等待上一个包被完整安装才会进入下一个；Yarn 则并行的执行这些任务，提高了性能。

#### Yarn vs npm: CLI 的差异
除了一些功能差异，Yarn 命令也存在一些区别。例如移除或修改了一些 npm 命令以及添加了几个有趣的命令。
- yarn global
不像 npm 添加 -g 或 --global 可以进行全局安装，Yarn 使用的是 global 前缀。不过与 npm 类似，项目依赖不推荐全局安装。

global 前缀只能用于 yarn add, yarn bin, yarn ls 和 yarn remove，除yarn add 外，这些命令都和 npm 等效。

- npm install
npm install 命令会根据 package.json 安装依赖以及允许你添加新的模块；yarn install 仅会按 yarn.lock 或 package.json 里面的依赖顺序来安装模块。

- yarn add [–dev]

与 npm install 类似，yarn add 允许你添加与安装模块，就像命令的名称一样，添加依赖意味着也会算定将依赖写入 package.json，类似 npm 的 --save 参数；Yarn 的 --dev 参数则是添加开发依赖，类似 npm 的 --save-dev 参数。

- yarn licenses [ls|generate-disclaimer]

npm 没有类似命令来方便编写自己的包。yarn licenses ls 列出所有已安装包的许可协议。yarn licenses generate-disclaimer 生成包含已安装包许可协议的免责声明。某些协议要求使用者必须在项目中包含该协议，这时候该命令将变得非常好用。

- yarn why

该命令会查找依赖关系并找出为什么会将某些包安装在你的项目中。也许你明确为什么添加，也许它只是你安装包中的一个依赖，yarn why 可以帮你弄找出。

- yarn upgrade

该命令会根据符合 package.json 设定的规则而不是 yarn.lock 定义的确切版本来将包更新到最新版本。如果想用 npm 来实现相同目的，可以这样执行：
```
rm -rf node_modules
npm install
```
不要将该命令与 npm update 混淆，它指的是更新到自己的最新版。

- yarn generate-lock-entry

yarn generate-lock-entry 会基于 package.json 设置的依赖生成 yarn.lock 文件，该命令与 npm shrinkwrap 类似，但应该小心使用，因为通过 yarn add 和 yarn upgrade 命令添加或更新依赖时会自动更新生成该锁文件。

#### 快速安装
npm install -g yarn
#### 常用命令快速对比
npm install  => yarn install
npm install --save => yarn add
npm install --save-dev => yarn add -dev
#### 针对国内的设置
如果没有出现网络问题，请直接忽略下面的内容。如果出现错误可以考虑设置一下npm源，代码如下
```
yarn config set registry https://registry.npm.taobao.org
```
