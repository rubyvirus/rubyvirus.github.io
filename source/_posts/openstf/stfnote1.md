---
title: openstf二次开发之分析源码第一篇(登录组件)
tag: open-stf
---

## 前言
前面我们已经了解了openstf的安装、编译，现在我们就开始分析源码，openstf提供了两种登录方式如下：
1. ldap
2. mock

看[openstf开发文档](https://github.com/openstf/stf/blob/master/doc/DEPLOYMENT.md#stf-authservice)说明还提供了OAuth2.0，似乎只对Docker deployment.下面以mock为例，简单说明一下。

## 首先介绍下openstf整体目录结构
├── bin ---------------------------------------------- 启动目录
├── bower.json --------------------------------------bower包管理配置文件
├── CHANGELOG.md--------------------------------变更说明
├── CONTRIBUTING.md
├── doc------------------------------------------------文档说明
├── docker--------------------------------------------Docker镜像
├── Dockerfile----------------------------------------Docker配置文件
├── DONATION-TRANSPARENCY.md
├── gulpfile.js----------------------------------------gulp自动构建文件
├── ISSUE_TEMPLATE.md
├── lib ------------------------------------------------后端项目
├── LICENSE
├── node_modules
├── package.json
├── README.md
├── res------------------------------------------------前端项目
├── test ----------------------------------------------检测项目
├── TESTING.md
├── tmp
├── vendor--------------------------------------------安装目录
├── webpack.config.js
└── yarn.lock
note: 目录成树桩显示，用的是tree -L 1 命令

open-stf用的是模块化开发，通过webpack.config.js可以知道app, authldap, authmock三个入口，即模块
## mock登录
### 1. mock前端部分
路径：/stf/res/auth/mock
├── scripts
│   ├── entry.js
│   └── signin
│       ├── index.js
│       ├── signin-controller.js
│       ├── signin.css
│       └── signin.pug
└── views
    └── index.pug
- 首先需要了解的是entry.js(webpack)入口配置点，[webpack详细配置说明](http://www.css88.com/doc/webpack2/guides/code-splitting-require)
包含两个部分，
  一是依赖的模块
  require('nine-bootstrap')
  require('angular')
  require('angular-route')
  require('angular-touch')
  require('./signin').name >> 首页模块
  二是angular模块的初始化和route配置
  ```
  $locationProvider.html5Mode -开启html5
  $routeProvider
        .otherwise({
          redirectTo: '/auth/mock/'
        })
        配置跳转路由
  ```
  通过entry.js的配置，我们知道页面将跳转到'/auth/mock'
- 


### 2. mock后端部分
路径：/stf/lib/units/auth
├── ldap.js
├── mock.js
├── oauth2
│   ├── index.js
│   └── strategy.js
├── openid.js
└── saml2.js
mock.js为mock部分后端源码
