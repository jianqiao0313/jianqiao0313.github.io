---
title: Angular2-busy引发的Cannot find module(ng build --aot)
date: 2017-07-25 09:02:50
tags: Angular4.0
categories: Angular4.0
---
# 背景
最近写Angular4有这么一个需求，当发送post请求还没返回时（后台处理这个请求需要3-5秒的时间），页面显示一个蒙版，不让用户操作页面，如点击按钮之类的操作。
# 问题
当运行ng build --aot --prod后，部署到nodejs服务器上，点击lazy类型的路由，就报错，cannot find module，如下图：

![image](https://static.gezichenshan.top/blog/angular4-3-1.png)
当只运行ng build的时候，一切正常
# 环境

```
"@angular/animations": "^4.0.0",
"@angular/cli": "^1.1.2",
"@angular/common": "^4.0.0",
"@angular/compiler": "^4.0.0",
"@angular/core": "^4.0.0",
"@angular/forms": "^4.0.0",
"@angular/http": "^4.0.0",
"@angular/platform-browser": "^4.0.0",
"@angular/platform-browser-dynamic": "^4.0.0",
"@angular/router": "^4.0.0",
"angular2-busy": "^2.0.4",
"classlist.js": "^1.1.20150312",
"core-js": "^2.4.1",
"intl": "^1.2.5",
"rxjs": "^5.1.0",
"web-animations-js": "^2.2.5",
"zone.js": "^0.8.4"
```

```
node -v
v6.11.0
```
# 路由配置
//app.routes.ts

```
{
    path: 'application',
    loadChildren: './application/application.module#ApplicationModule'
},
```
//application.routes.ts

```
import {AuditedApplicationComponent} from "./audited-application/audited-application.component";
import {AuditingApplicationComponent} from "./auditing-application/auditing-application.component";
import {AuditingDetailComponent} from "./auditing-detail/auditing-detail.component";
import {AuditedDetailComponent} from "./audited-detail/audited-detail.component";
import {DealingDetailComponent} from './dealing-detail/dealing-detail.component';
import {DealingApplicationComponent} from './dealing-application/dealing-application.component';

export const applicationRoutes = [
  {
    path: 'auditedApplication',
    component: AuditedApplicationComponent
  },
  {
    path: 'auditingApplication',
    component: AuditingApplicationComponent
  },
  {
    path: 'auditingDetail',
    component: AuditingDetailComponent
  },
  {
    path: 'auditedDetail',
    component: AuditedDetailComponent
  },
  {
    path: 'dealingApplication',
    component: DealingApplicationComponent
  },
  {
    path: 'dealingDetail',
    component: DealingDetailComponent
  }
];
```
# 问题原因
是angular2-busy这个插件的问题，当build时加上 --aot选项，就会报这个错。
# 解决方法
删除这个插件，--aot就不会报错了。
# 尝试过的方法
## 引用nikhiln写的angular2-busy插件
我在查angular2-busy的issue的时候，有一哥们叫nikhiln,他说他fork了angular2-busy，并且修复了aot的问题。我把我项目里node_modules下的angular2-busy代码替换成他写的，但问题依然存在。

> [nikhiln | angular2-busy](https://github.com/nikhiln/angular2-busy)

## 在stackoverflow上搜索
![image](https://static.gezichenshan.top/blog/angular4-3-3.png)
> [lazyloader does not works on AOT production mode angular-cli](https://stackoverflow.com/questions/44163737/lazyloader-does-not-works-on-aot-production-mode-angular-cli)

## 查看angular2-busy的commit记录
我找到一条他最近提交的记录，如下图，说修复了aot的问题，但aot时还有bug。插件最新的版本就是2.0.4，而我的版本也是2.0.4。
![image](https://static.gezichenshan.top/blog/angular4-3-3.png)
# 结论
还是因为这个插件aot的时候出问题，有时间我会尝试着改改，下次还是自己实现一个蒙版的插件吧。

> [angular2-busy插件Github地址](https://github.com/devyumao/angular2-busy)