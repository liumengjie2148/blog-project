---
title: MacOS安装npm全局包遇到权限问题
date: 2018-08-27 15:30:11
tags:
---
## MacOS安装npm全局包遇到权限问题

在MacOS上全局安装某个npm包时，会遇到如下问题：

```javascript
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules
```

**解决方案：**

在安装命令前加上`sudo`,即执行命令 `sudo npm install -g npm包名`，
会提示输入密码，输入本机开机密码即可获取权限安装。