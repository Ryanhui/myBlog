---
title: 如何为 React 项目配置 ESlint
date: 2019-03-19 10:24:53
tags: 前端
---

1. 在项目根目录 （package.json）在的位置，执行以下命令：

    ```
    npx install-peerdeps --dev eslint-config-airbnb
    ```
    如上命令会安装 ESlint 和其他依赖

2. 同样继续在项目根目录执行以下命令：
   
   ```
   npm install babel-eslint --dev-save
   ```
   由于 eslint-config-airbnb 对 JSX 支持不全，所以需要 babel-eslint 补充

3. 初始化 ESlint ，继续在根目录执行：
   ```
   eslint --init
   ```
   之后会有一些配置选项，根据需要选择即可

4. 完成后，根目录会出现 .eslintrc.js 文件，编辑此文件
   
   主要 添加如下两行：
   ```
   "extends": "airbnb",
   "parser": "babel-eslint",
   ```
   可以参考以下代码：
   ```
   module.exports = {
    "env": {
        "browser": true,
        "es6": true
    },
    "extends": "airbnb",
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    "parser": "babel-eslint",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 2018,
        "sourceType": "module"
    },
    "plugins": [
        "react"
    ],
    "rules": {
        "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
    }
   };
   ```

  5. 最后，在 vs code 设置中开启 eslint 相关功能。
   
      设置按钮 - setting - extensions - ESlint

      勾选 ESlint: auto Fix on save 以及 ESlint: enable