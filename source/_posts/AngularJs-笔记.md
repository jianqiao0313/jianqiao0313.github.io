---
title: AngularJs 1.3.0版本 笔记
date: 2017-04-20 16:59:10
tags: Angular 
categories: JS
---
# AngularJS笔记
## 获取scope对象

```
var appElement = document.querySelector('[ng-controller=UserContractCtrl]');
var $scope = angular.element(appElement).scope();
```

## ng-class的用法

```
<label ng-class="{'aaa' : 'labelcheck1' ,'bbb' : 'labelcheck2' , 'ccc' : 'labelcheck3'}[label]">
//label为String类型,根据它的值来判断添加哪个class
```

## select的用法

```
//JS
$scope.searchOption = [
        { name: '申请代码', value: 'applicationCode' },
        { name: '申请人', value: 'applicantName' },
        { name: '公司', value: 'applicantCompany' },
        { name: '申请人身份证', value: 'applicantCredentialCode' }
    ];
$scope.searchSelect = $scope.searchOption[0];
```

```
//HTML
<select ng-model="searchSelect" ng-options="temSearchOption.name for temSearchOption in searchOption"></select>
```

如果选中申请人，那么$scope.searchSelect.value就等于'applicantName'

## ui-bootstrap pagination的用法
[angular-ui](http://angular-ui.github.io/bootstrap/) 这个网页得翻墙访问，用国内的网访问的特别慢
```
//JS
var app = angular.module('myApp', ['ui.bootstrap']);
```

```
//HTML
<ul uib-pagination previous-text="上一页" next-text="下一页" first-text="首页" last-text="末页" total-items="total" boundary-links="true" items-per-page="pageSize" ng-model="currentPage" ng-change="pageChanged()" max-size="5" class="pageUl"></ul>
//boundary-links 代表是否显示首页和尾页按钮
```
# 推荐几个好用的angular的插件
## angular-ui
> 官网: [angular-ui](http://angular-ui.github.io/bootstrap/)

> Github: [angular-ui](https://github.com/angular-ui/bootstrap) 

如果写angular想用bootstrap的话，直接照着例子用，比较方便，而且官网给的例子还有plunker（可以在线调试、运行的网站，唯一不好的就是得翻墙）。github上很活跃，有14000+的星。

## ngSweetAlert
> Github: [ngSweetAlert](https://github.com/oitozero/ngSweetAlert) 

oitozero这哥们把挺有名的alert插件sweetAlert给封装了一下，使用方法也很简单。（注：[sweetAlert](https://github.com/t4t5/sweetalert) 这个插件很火，而且支持IE8，github上有15000+的星）

### 使用方法
```
var app = angular.module('myApp', ['oitozero.ngSweetAlert']);
app.controller('yourController', ['$scope', 'SweetAlert', function($scope, SweetAlert) {
    SweetAlert.swal('HelloWord', 'welcome jianqiao0313blog' , 'success');
}]);
```

## loading-bar
> 官网: [angular-loading-bar](https://chieffancypants.github.io/angular-loading-bar)

> Github: [angular-loading-bar](https://github.com/chieffancypants/angular-loading-bar)

这个插件是在发送get、post请求的时候，自动执行的。可以通过css修改样式（loading-bar.css），默认样式是左上角有个圈圈，页面上头有个读条。

### 使用方法
```
var app = angular.module('myApp', ['angular-loading-bar']);
```

## validation
> 官网: [validation](http://hueitan.github.io/angular-validation/)

> Github: [validation](https://github.com/hueitan/angular-validation)

这是做表单验证的时候用到的，可以自己写正则规则。需要注意在使用的时候，input必须有name，否则不验证。
### 使用方法
```
//JS
var app = angular.module('myApp', ['validation', 'validation.rule']);
```


```
//HTML
<form name="Form"><!-- 一定要加表单 -->
    <input id="userName"  type="text" ng-model="userName"  name="userName"  validator="required"/><!-- 用户名不能为空 -->
    <input id="userPhone" type="text" ng-model="userPhone" name="phone"     validator="required,phone"/><!-- 手机号不能为空并且必须是合法手机号 -->
    <input id="yourrules" type="text" ng-model="yourrules" name="yourrules" validator="required,yourrules"/><!-- 不能为空并且满足yourrules规则 -->
                               
    <button validation-submit="Form" ng-click="save()">保存</button><!-- 先进行表单验证，后执行save方法 -->
    <button validation-reset="Form"  ng-click="cancel()">取消</button>
</form>
```
