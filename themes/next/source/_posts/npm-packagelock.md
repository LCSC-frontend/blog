---
title: 理解 npm5 中的 package-lock.json 文件
tags: [npm, package-lock, js]
images:
---
npm是一个用于管理package之间依赖关系的管理器，它允许开发者在package.json中标出自己项目对各库包的依赖。在npm5开始，当使用npm时会自动生成一个package-lock.json文件，它是做什么用的呢？

<!-- more -->

## package.json 和 package-lock.json 的概念

### 语义化版本控制规范（Semver）

在说相关内容之前，先了解一下Semver。它是什么呢？

简介上是这样的：

> 在软件管理的领域里存在着被称作“依赖地狱”的死亡之谷，系统规模越大，加入的套件越多，你就越有可能在未来的某一天发现自己已深陷绝望之中。
>
> 用一组简单的规则及条件来约束版本号的配置和增长被提出来。这些规则是根据（但不局限于）已经被各种封闭、开放源码软件所广泛使用的惯例所设计。
> 
> 在这套约定下，版本号及其更新方式包含了相邻版本间的底层代码和修改内容的信息。
>

![semver](/images/npm-packagelock/semver1.png)

相关链接：https://semver.org/lang/zh-CN/

### package.json 的使用

开发者在使用npm管理器时，会在package.json中标出自己项目对各库包的依赖，比如：

```
"dependencies": {
  "vue": "^2.4.2"
}
```

我们注意到，这里版本前面有个“^”，它表示的意思是“向后/新兼容”。在不超过大版本“2”的前提下，允许下载最新版本，所以如果在package.json中写了这样一条依赖，实际运行npm install时，可能下载的版本并不是 2.4.2 ，而是 2.5.0 之类的更新的版本。

此外还有在版本号前加“~”的，与“^”不同的是，“^”是锁定了大版本号而已，而“~”是锁定了大版本号和次版本号，只允许对修订号不锁定。

大版本号是大修改情况下会更新，一般这种情况下api可能会发生改变；次版本号一般是功能的补充等，api不变；修订号一般是对bug的修复。

所以说，一般情况下，这种向新兼容是没有问题的，不会因为依赖的库的更新也使得我们自己的项目出现error，同时，库自己的bug也可以在我们每次打包的时候，悄无声息地自己随着新的版本被修复了。

当然，完全相同的代码库，在不同时间或者不同npm下载源下，是可能会出现下到的依赖包是不同的，也就是说，你跟你的同事跑同一套代码，但是在你本地上没有任何问题，在他那里却出现了warning之类的情况，可能就是你们其实下载的不是同一个版本的包，而这可能会对项目的运行产生影响。

同时比较值得注意的是，现在semver规范只是一种约定，并不是所有模块都遵循semver语义化版本。（这告诉我们，找模块一定要找靠谱的！）

但是有一些程序员觉得，“与其相信别人，我只相信自己”

所以出现了“锁定版本号”这种现象，关于npm的锁版本号，有两个东西可以提一提： npm-shrinkwrap 和 package-lock 。

## 锁定版本号

### 手动控制？

可能有人说，不是说“^”允许下载更新的版本，那直接修改 package.json 让依赖的模块不让修改可以吗？

好像可以。但是！

比如说，我们依赖了包A，而包B依赖了包C：

```
A@0.1.0
  B@0.0.2
    C@0.0.1
```

你的项目直接依赖只有A，精确控制版本号也只能控制A，那么多层依赖中的B，C，...呢？

程序员怎么可能做这种事╮(╯▽╰)╭

### npm-shrikwrap 命令

其实早在 npm2 的时候，npm 就提供了 shrinkwrap 命令来解决这个问题：

```
npm shrinkwrap
```

shrinkwrap 会在根目录生成 npm-shrinkwrap 文件，之后的 npm install 会参照这个文件的版本来安装。而 shrinkwrap 是根据当前安装的目录结构生成的。所以如果你不能保证 package.json 文件定义的依赖与 node_modules 下已安装的依赖是匹配的，无冗余的，可以在执行 shrinkwrap 命令前清理依赖并安装。

shrinkwrap.json 文件与后来的 package.json 有大致一样的地方，可以一起在 package-lock.json 中看。

### package-lock.json

在 npm5 的时候，终于出现了 package.json （因为 yarn 产生的危机感？）。目的是，只要保存了源文件，只要按照这个 package-lock,json 所标示的具体版本下载依赖包，就能确保所有库包与你上次安装的完全一样。

这个文件大致的依赖这样来表示：

```
...
 "acorn-dynamic-import": {
      "version": "2.0.2",
      "resolved": "https://registry.npmjs.org/acorn-dynamic-import/-/acorn-dynamic-import-2.0.2.tgz",
      "integrity": "sha1-x1K9IQvvZ5UBtsbLf8hPj0cVjMQ=",
      "dev": true,
      "requires": {
        "acorn": "4.0.13"
      },
      "dependencies": {
        "acorn": {
          "version": "4.0.13",
          "resolved": "https://registry.npmjs.org/acorn/-/acorn-4.0.13.tgz",
          "integrity": "sha1-EFSVrlNh1pe9GVyCUZLhrX8lN4c=",
          "dev": true
        }
      }
    },
  ...
```

“version”：版本号，
“resolved”：该依赖包的压缩包下载地址，
“integrity”： 验证已安装的包是否被改动过，（我看着像是一种加密方式，但是具体怎么验证我也不是很懂）
“dependencies”： 该依赖包依赖的包

shrinkwrap 与 package-lock 的 json 文件大致是相同的，那么不同的地方在哪里？

1. package-lock 每次 npm 操作都会更新；而 shrinkwrap.json 只有执行该命令时才会生成；
2. package-lock 只作用于当前项目不会发布;而 npm-shrinkwrap 可以发布并在所有安装它的地方都生效；
3. 同时使用 package-lock.json 和 npm-shrinkwrap.json ， package-lock.json 将被忽略。

所以一般而言， package-lock.json 和 npm-shrinkwrap 使用场景：

1. package-lock.json 可以用在开发环境下，保证不同开发人员的依赖是一致的；
2. npm-shrinkwrap.json 可以在打包前生成，随包发布，来锁定版本，如果你真的想要的话。

## 番外

### npm install 的过程

1. 找到直接依赖的模块；
2. 如果有 npm-shrinkwrap.json 文件或者 package-lock.json 文件，则在其中获取版本信息，没有的话，则在 package.json 中获取；
3. 先会去本地缓存（~/.npm）中找这个模块。如果没有找到，就根据压缩包地址，下载模块压缩包，存放在~./npm（cache）中；
4. 查找再下一层依赖；
5. dedupe依赖树，去掉重复依赖的模块；
6. 安装模块，存在 node_modules 文件夹中。

### Registry

registry 是 npm 模块仓库提供的一种查询服务，用户通过 npm 命令下载并安装指定模块，也可以通过 npm 将自己设计的模块放到 registry 上。

npm 的 registry 没有审核机制，因此会存在一些低质量、不安全甚至有害的模块，不过 npm 服务器的管理员也可以删除有害模块并阻止不怀好意的用户。
