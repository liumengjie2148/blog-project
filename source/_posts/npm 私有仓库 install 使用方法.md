---
title: npm 私有仓库 install 使用方法
date: 2018-07-14 15:08:25
tags:
---

## 创建用户

``` shell
npm adduser --registry=http://registry.npm.aibeike.com:7001
```

![](http://xuhongbo.qiniudn.com/WX20180815-174640@2x.png)

## 登录用户并指定 registry（源）和scope（作用域）

```shell
npm login --registry=http://registry.npm.aibeike.com:7001 --scope=@tal
--registry # 私有仓库地址
--scope    # 作用域，当带有配置的作用域时，会到我们的私服上拉取npm包
```

*npm包以后的命名会有作用域，规范 `@scope/packageName`*

![](http://xuhongbo.qiniudn.com/WX20180815-175537@2x.png)



*注意请不要把全局的 registry设置为`http://registry.npm.aibeike.com:7001`*
