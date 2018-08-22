---
layout: 关于
title: package-lock 你想知道的一切
date: 2018-08-21 17:45:18
tags: 前端 分享
---

## [文章原文地址](https://medium.com/coinmonks/everything-you-wanted-to-know-about-package-lock-json-b81911aa8ab8)

## 前言

在npm升级到5.x.x以后，会自动在目录下生成一个名为Package-lock.json的文件。文件内容与package.json相似，但更为详细。虽然可以放任这个文件不管，但是时间长了就可能会出现依赖丢失或者安装错误版本依赖的情况。我们可以删除 package-lock.json 这个文件重新 npm install 。不过，这样似乎不妥。

## 总体而言

* 如果你使用的是npm ^5.x.x ，package-lock.json 文件会自动生成
* 应该使用package-lock文件保证安装的一致性和依赖的兼容性
* 千万不要把package-lock提交到你的版本控制系统
* 自从npm ^5.1.x 起，package.josn 文件能够修饰 package-lock.json 文件了，所以问题会少一些。
* 不再需要删除package-lock文件了，只要 npm install 即可
* 如果你的应用提供了API，使用semver，并且遵守semver的规则

## 背景

### 版本控制

在了解 package-lock 和 package.json 之前，首先要了解版本控制。如果你正构建一个应用有对外的接口，你应该表明你的改动会对第三方调用造成怎样的影响。通过版本控制，这轻而易举。版本号由三部分构成： X,Y,Z 分别代表主要的，次要的，补丁版本。补丁版本改变代表bug修复，不会影响任何事情。次要版本修改代表新功能添加，不会对固有功能影响。主版本的修改则代表着兼容性被打破。如果使用者不对主版本修改做适配，问题就很严重了。

### 包管理

你的项目可能有上百个依赖，而每一个依赖都有数量不少的依赖。为了不陷入依赖地狱，npm因此诞生。

当你安装一个包的时候，入口被添加到package.json文件，包含名字及版本。在版本定义中，npm还支持一些通配符。npm默认安装最新版本，然后创建一个标记，例如 ^2.1.12 代表最低版本，但是主版本号相同的高版本都可以使用。毕竟，次版本和补丁版本都是安全的。

### 项目分享

利用package.json真正的好处是，任何人都能通过这个文件安装依赖让你的应用跑起来。但是意外时常发生。

比如我们创建了一个使用express的项目，在我使用 npm install express — save 安装的时候express的最新版本是4.15.4。那么^4.15.14 的版本号就被添加到package.json文件中。不过，express的维护者在明天修复了一个bug，最新的版本号变为^4.15.15。这时有人想为你的代码提供贡献，当他clone了你的代码，npm install的时候，因为express的最新版本变味了^4.15.15，他安装的依赖也成为了^4.15.15。理论上讲，这应该兼容，但是可能express的维护者在发布^4.15.15版本时改出了一个问题，那么你和你的代码贡献者使用的express将会不同。

### Package-lock

### 目标

package-lock 就是为了解决上述问题而生的。

### 格式

指定的版本，模块的地址（URL），模块的hash值，包的requires，包的依赖，这些组成了package-lock文件

```json
"express": {
      "version": "4.15.4",
      "resolved": "https://registry.npmjs.org/express/-/express-4.15.4.tgz",
      "integrity": "sha1-Ay4iU0ic+PzgJma+yj0R7XotrtE=",
      "requires": {
        "accepts": "1.3.3",
        "array-flatten": "1.1.1",
        "content-disposition": "0.5.2",
        "content-type": "1.0.2",
        "cookie": "0.3.1",
        "cookie-signature": "1.0.6",
        "debug": "2.6.8",
        "depd": "1.1.1",
        "encodeurl": "1.0.1",
        "escape-html": "1.0.3",
        "etag": "1.8.0",
        "finalhandler": "1.0.4",
        "fresh": "0.5.0",
        "merge-descriptors": "1.0.1",
        "methods": "1.1.2",
        "on-finished": "2.3.0",
        "parseurl": "1.3.1",
        "path-to-regexp": "0.1.7",
        "proxy-addr": "1.1.5",
        "qs": "6.5.0",
        "range-parser": "1.2.0",
        "send": "0.15.4",
        "serve-static": "1.12.4",
        "setprototypeof": "1.0.3",
        "statuses": "1.3.1",
        "type-is": "1.6.15",
        "utils-merge": "1.0.0",
        "vary": "1.1.1"
      }
    },
```

同样，requires里的每个package都能找到入口信息。

随后，人们不再使用package.json解决安装包，而是使用package-lock.json。因为它指定了所有需要的包的版本，包下载地址，哈希值。每次安装依赖的结果必然相同，无论何时何地。

## 争议的存在

虽然package-lock解决了一个普遍的问题，但是为什么还是有很多人推崇禁用它或者质疑它是否真的好用。

在npm 5.x.x以前，人们习惯了使用package.json维护项目的依赖。但是当package-lock出现时，它跟人们预期的相反，修改package.json并不会反映到package-lock上。

比如：一个包在package和package-lock都有记录，手动修改了package里的版本号然后npm install，相应的包并不会安装，因为有package-lock里仍然为旧版本。

再比如：一个包只存在于package-lock中，另一个开发者根据package.json安装依赖就会丢失这个包。

人们不知道哪里出了问题，要不就是删除package-lock重新npm install，要不就是直接禁用package-lock.

问题的源头就是到底哪个文件才是决定依赖树的选择在左右摇摆，好在npm的维护人员决定让对package.json文件的更改自动与package-lock文件同步，发布于npm v5.1.0。