---
title: AngularJs 1.3.0版本 笔记
date: 2017-04-20 16:59:10
tags: Angular 
categories: JS
---
## 1. 获取scope对象

```
var appElement = document.querySelector('[ng-controller=UserContractCtrl]');
var $scope = angular.element(appElement).scope();
```

## 2. ng-class的用法

```
<label ng-class="{'aaa' : 'labelcheck1' ,'bbb' : 'labelcheck2' , 'ccc' : 'labelcheck3'}[label]">
//label为String类型,根据它的值来判断添加哪个class
```

