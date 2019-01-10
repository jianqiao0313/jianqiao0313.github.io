---
title: Angular4 IE9 文件上传--记一次捣鼓经历
date: 2017-06-23 16:56:40
tags: Angular4.0
categories: Angular4.0
---
# 需求
在IE9上，实现单个文件的上传。
<!-- more -->
# 环境
- "@angular/animations": "^4.1.3",
- "@angular/common": "^4.0.0",
- "@angular/compiler": "^4.0.0",
- "@angular/core": "^4.0.0",
- "@angular/forms": "^4.0.0",
- "@angular/http": "^4.0.0",
- "@angular/platform-browser": "^4.0.0",
- "@angular/platform-browser-dynamic": "^4.0.0",
- "@angular/router": "^4.0.0",
- "@ng-bootstrap/ng-bootstrap": "^1.0.0-alpha.26",
- "bootstrap": "^4.0.0-alpha.2",
- "classlist.js": "^1.1.20150312",
- "core-js": "^2.4.1",
- "intl": "^1.2.5",
- "ng2-file-upload": "^1.2.1",
- "rxjs": "^5.1.0",
- "web-animations-js": "^2.2.5",
- "zone.js": "0.8.5"

# 出现的问题
IE9报错：SCRIPT438: 对象不支持"apply"属性或方法

英文：Object doesn't support property or method 'apply'

图片：![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/Angular4-2-1.jpg)

# 出现问题的代码

```
//upload-cert.service.ts
public uploadCert(file:File){
    let formData:FormData = new FormData();
    formData.append('uploadFile' , file , file.name);
    let headers= new Headers();
    headers.append('Content-Type', 'multipart/form-data');
    headers.append('Accept', 'application/json');
    let options = new RequestOptions({ headers: headers });
    return this.http
      .post(this.certUrl , formData , options)
      .map((res: Response) => {
          console.log(res);
      })
  }
```


```
//upload-cert.component.ts
uploadCert(){
    if(this.formErrors.certFormError == '' && this.certFile){
        this._passwordAndUploadCert.uploadCert(this.certFile)
         .subscribe(
           data => console.log(data+'-----'),
           error => console.log(error+'error!!!')
        )
    }
```

**PS:只有文件上传IE9出问题，正常的post、get请求，在IE9上没问题。**

# 解决方法

在Angular 4 里面引入Jquery，然后上传就可以了，我用的是ajaxfileupload.js这个插件。
> [ajaxfileupload-github](https://github.com/carlcarl/AjaxFileUpload/blob/master/ajaxfileupload.js)

## 代码如下
1. index.html引入js

```
<script src="assets/js/jquery-1.11.min.js"></script>
<script src="assets/js/ajaxfileupload.js"></script>
```

2. 在upload-cert.component.ts里面 添加declare


```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder, Validators, ReactiveFormsModule } from '@angular/forms';
import { PasswordFormModel } from './password-model';
import { PasswordAndUploadCertService } from './password-and-upload-cert.service';

declare var jquery :any;
declare var $ :any;

@Component({
  selector: 'app-password-and-upload-cert',
  templateUrl: './password-and-upload-cert.component.html',
  styleUrls: ['./password-and-upload-cert.component.css']
})
export class PasswordAndUploadCertComponent implements OnInit {
    //上传证书
    uploadCert(){
        $.ajaxFileUpload({
            url:'upload',
            secureuri:false,
            fileElementId:'upload_input',
            dataType: 'json',
            success: function (ret, status){
             console.log("okok");
            },
            error: function (data, status, e) {
              console.log("error");
            }
        });
    }
}
```

# 尝试过的方法
## 根据Angular官网引入一大堆填充库的js
> [浏览器支持](https://angular.io/guide/browser-support)

我的index.html现在长这样

```
<!doctype html>
<html>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Fido</title>
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link rel="stylesheet" href="assets/css/bootstrap.min.css">
  <link rel="stylesheet" href="assets/css/font-awesome.min.css">
  <link rel="stylesheet" href="assets/css/reset.css">
  <link rel="stylesheet" href="assets/css/index.css">

  <script src="assets/js/shim.min.js"></script>
  <script src="assets/js/classList.min.js"></script>
  <script src="assets/js/Blob.js"></script>
  <script src="assets/js/formdata.js"></script>
  <script src="assets/js/Intl.min.js"></script>
  <script src="assets/js/typedarray.js"></script>
  <script src="assets/js/core.min.js"></script>
  <script src="assets/js/es6-shim.min.js"></script>
  <script src="assets/js/shims_for_IE.js"></script>
  <script src="assets/js/system-polyfills.js"></script>
  <script src="assets/js/jquery-1.11.min.js"></script>
  <script src="assets/js/ajaxfileupload.js"></script>

</head>
<body>
  <app-root>Loading...</app-root>
</body>
</html>

```

没错，全是google或者Stack Overflow上搜索到的解决方法。不能说没用，确实有点用。

## polyfills.ts所有注释全部取消
用angular-cli生成的项目有polyfills.ts这个文件，我把所有注释都去了，安装了所有的填充库，代码如下：

```
/**
 * This file includes polyfills needed by Angular and is loaded before the app.
 * You can add your own extra polyfills to this file.
 *
 * This file is divided into 2 sections:
 *   1. Browser polyfills. These are applied before loading ZoneJS and are sorted by browsers.
 *   2. Application imports. Files imported after ZoneJS that should be loaded before your main
 *      file.
 *
 * The current setup is for so-called "evergreen" browsers; the last versions of browsers that
 * automatically update themselves. This includes Safari >= 10, Chrome >= 55 (including Opera),
 * Edge >= 13 on the desktop, and iOS 10 and Chrome on mobile.
 *
 * Learn more in https://angular.io/docs/ts/latest/guide/browser-support.html
 */

/***************************************************************************************************
 * BROWSER POLYFILLS
 */

/** IE9, IE10 and IE11 requires all of the following polyfills. **/
 import 'core-js/es6/symbol';
 import 'core-js/es6/object';
 import 'core-js/es6/function';
 import 'core-js/es6/parse-int';
 import 'core-js/es6/parse-float';
 import 'core-js/es6/number';
 import 'core-js/es6/math';
 import 'core-js/es6/string';
 import 'core-js/es6/date';
 import 'core-js/es6/array';
 import 'core-js/es6/regexp';
 import 'core-js/es6/map';
 import 'core-js/es6/set';

/** IE10 and IE11 requires the following for NgClass support on SVG elements */
 import 'classlist.js';  // Run `npm install --save classlist.js`.

/** IE10 and IE11 requires the following to support `@angular/animation`. */
 import 'web-animations-js';  // Run `npm install --save web-animations-js`.


/** Evergreen browsers require these. **/
import 'core-js/es6/reflect';
import 'core-js/es7/reflect';


/** ALL Firefox browsers require the following to support `@angular/animation`. **/
 import 'web-animations-js';  // Run `npm install --save web-animations-js`.



/***************************************************************************************************
 * Zone JS is required by Angular itself.
 */
import 'zone.js/dist/zone';  // Included with Angular CLI.



/***************************************************************************************************
 * APPLICATION IMPORTS
 */

/**
 * Date, currency, decimal and percent pipes.
 * Needed for: All but Chrome, Firefox, Edge, IE11 and Safari 10
 */
 import 'intl';  // Run `npm install --save intl`.
/**
 * Need to import at least one locale-data with intl.
 */
 import 'intl/locale-data/jsonp/en';

```

反正我引入后觉得没啥区别，之前Post、Get请求也是IE9出问题，但是我在index.html引入了那些填充库后，Post、Get就正常了。

# 写在最后
现在觉得Angular4越来越好用了，有一些IE9支持不友好的功能，完全可以通过Jquery插件的形式实现，毕竟Jquery火了这么久，有好多高质量的插件。写Angular的时候如果要考虑IE9及以上，不要进入只查文档、google的胡同里，记住你还有Jquery呢。

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/Angular4-2-2.jpg)

# 对了
最后推荐一个Github仓库

> [Github-一个基于Angular4+Bootstrap4+Scss的后台管理系统界面](https://github.com/332557712/cc)

哥们是个中国人，QQ号是332557712，因为BS的模态框使用中有问题找他QQ请教了一下，人很好，点个赞！
