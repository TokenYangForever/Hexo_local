title: 使用eslint检查JS代码
author: TokenYangForever
tags:
  - JavaScript
  - 工具
categories:
  - JavaScript
date: 2017-10-11 16:13:00
---
#### 前言 ####
* 代码规范一直是开发过程中比较重要的一环，包括命名规范、统一缩进等等，规范整洁的代码可读性高，也便于后期代码维护以及其他开发人员快速熟悉；本篇就讲一下eslint的具体用法。

#### 介绍 ####
* eslint早在2013年就有了，而我个人第一次接触还是在使用vue-cli搭建项目的时候，默认配置的eslint会根据规则直接报错，刚开始用的时候满屏报错、特别酸爽~~ 但只要习惯就好，使用eslint检查代码会使你的js文件更加健壮好看。

#### 安装配置 ####
* 首先安装eslint，直接 **npm install eslint**即可，注意如果你是全局安装的eslint，后面一些eslint相关依赖包也要全局安装。
* 然后输入 **eslint init** 开始初始化一个配置文件，过程中会给你很多选项，比如是否使用es6、是否使用jsx语法、配置文件的文件格式等等。我选择的生成js配置文件，所有选项都选好后，生成一个**.eslintrc.js**配置文件内容大概长这样：
```module.exports = {
    "env": {
        "browser": true,
        "es6": true
    },
    "extends": "eslint:recommended",
    "parserOptions": {
        "sourceType": "module"
    },
    "rules": {
        "indent": [
            "error",
            "tab"
        ],
        "linebreak-style": [
            "error",
            "windows"
        ],
        "quotes": [
            "error",
            "double"
        ],
        "semi": [
            "error",
            "never"
        ],
        "no-console": 0 
    }
};
```

* 其中最重要的就是**rules**里面配置的规则，eslint会根据这里是配置的rule规则对代码进行检验，键值分别对应规则名和状态，比如”no-console“是规则名，后面对应的0表示不执行这条规则。extends表示继承自哪个共享的配置文件，env表示检查代码时所属的环境。
* 具体状态码规则是这样：  **0**或者“**off**”表示关闭规则； **1**或者**warn**表示开启规则，使用警告级别的错误； **2**或者**error**表示开启规则，使用错误级别的错误：error (当被触发的时候，程序会报错)
* 配置完成后，在当前目录新建一个index.js文件，随便输入几行代码，**然后在命令行输入eslint index.js**。就会对js文件进行代码检查，如果代码报错，命令行会提示你多少行触发了哪条规则的错误，你就可以对代码进行改正，或者修改配置文件中的rule规则。
* 配置eslint的方法还有其他的，比如写在package.json中：新增一个eslintConfig，将上述配置文件的内容放在下面。但我个人还是喜欢用单独的配置文件，来控制eslint。
#### 集成到webpack ####
* 接下来介绍一下如何把eslint集成到webpack中。首先确保webpack已经安装好，然后依次安装eslint-loader、eslint-config-standard、eslint-plugin-html、eslint-plugin-promise、eslint-plugin-standard、eslint-friendly-formatter。注意如果eslint是全局安装的，这些依赖包也需要全局安装，否则报错找不到对应的包。
* **然后新建一个webpack的配置文件webpack.config.js**，我这里只是做尽量最少的配置，具体如下：
```
module.exports = {
    entry: {
        index: "./src/index.js",
    },
    output: {
        path: __dirname + "/dist/",
        filename: "[name].js"
    },
    resolve: {
        extensions: ['.js', '.vue', '.json']
    },
    module: {
         rules: [
                {
                    test: /\.js$/,
                    exclude: /node_modules/,
                    loaders: [
                        'eslint-loader'
                    ]
                },
                {
                    test: /\.vue|\.js$/,
                    enforce: 'pre',  // 在babel-loader对源码进行编译前进行lint的检查
                    include: /src/,  // src文件夹下的文件需要被lint
                    use: [{
                        loader: 'eslint-loader',
                        options: {
                            formatter: require('eslint-friendly-formatter')   // 编译后错误报告格式
                        }
                    }]
                }
            ]
    }
}
```
* 这时候文件目录结构如下图
![文件结构](http://upload-images.jianshu.io/upload_images/6383319-f6ea3f19ed2c3380.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 这时在src文件夹下新建index.js文件，命令行输入webpack进行代码打包的过程会使用eslint进行检查，如果无误就在dist/js文件夹下生产打包后的代码，如果检查报错则在命令行中打印出错误位置和触犯的规则。