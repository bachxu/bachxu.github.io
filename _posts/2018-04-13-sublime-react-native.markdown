---
layout: post
title:  "ST 中如何配置 ESLint 检测 React 代码"
date:   2018-04-13 08:45:37 +0800
categories: iTools
---
[React Native](https://facebook.github.io/react-native/) 通过使用 JavaScript 和 React 来构建手机原生应用。本文主要讲解了如何在 Sublime Text 中通过配置 ESLint 进行 React 代码静态分析。

### ESLint 介绍

[ESLint](https://eslint.org/) 最初是由 [Nicholas C. Zakas](https://www.nczonline.net/) 于2013年6月创建的开源项目。它的目标是提供一个插件化的javascript代码检测工具。

> 可组装的JavaScript和JSX检查工具

### Sublime Text 插件安装

- SublimeLiter
- SublimeLinter-eslint

### npm 组件安装

安装 npm 相关组件都在项目的根目录下进行。

1. linter 安装

    ```bash
    $ npm install linter
    ```

2. eslint 安装

    ```bash
    $ npm install eslint
    ```

3. eslint-plugin-react 安装，用于 React 中的一些语法检验

    ```bash
    $ npm install eslint-plugin-react
    ```

4. babel-eslint 安装，用于检测 es6 新语法

    ```bash
    $ npm install babel-eslint
    ```

### 创建检测配置文件

在项目根目录下创建 <strong>.eslintrc</strong> 文件，ESLint 会根据 .eslintrc 中定义的规则进行代码检测。ESLint 官方给出了一些[有关 React 配置的文档](https://github.com/yannickcr/eslint-plugin-react)。

以下是个人目前在项目中使用的配置：

```
{
    "env": {
        "browser": true,
        "es6": true,
        "node": true,
        "commonjs": true
    },
    "extends": "eslint:recommended",
    "parserOptions": {
        "ecmaFeatures": {
            "experimentalObjectRestSpread": true,
            "jsx": true,
            "arrowFunctions": true,
            "classes": true,
            "modules": true,
            "defaultParams": true
        },
        "sourceType": "module"
    },
    "parser": "babel-eslint",
    "plugins": [
        "react"
    ],
    "rules": {
        "linebreak-style": [
            "error",
            "unix"
        ],
        //"semi": ["error", "always"],
        "no-empty": 0,
        "comma-dangle": 0,
        "no-unused-vars": 0,
        "no-console": 0,
        "no-const-assign": 2,
        "no-dupe-class-members": 2,
        "no-duplicate-case": 2,
        "no-extra-parens": [2, "functions"],
        "no-self-compare": 2,
        "accessor-pairs": 2,
        "comma-spacing": [2, {
            "before": false,
            "after": true
        }],
        "constructor-super": 2,
        "new-cap": [2, {
            "newIsCap": true,
            "capIsNew": false
        }],
        "new-parens": 2,
        "no-array-constructor": 2,
        "no-class-assign": 2,
        "no-cond-assign": 2
    }
}
```

Follow your heart.
