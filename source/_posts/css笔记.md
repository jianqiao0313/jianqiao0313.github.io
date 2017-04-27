---
title: css笔记
date: 2017-04-26 09:32:48
tags: CSS
categories: CSS
---
# 据说是最好的清除浮动的方法


```
.clearfix:after {
    clear: both;
    content: ' ';
    display: block;
    font-size: 0;
    line-height: 0;
    visibility: hidden;
    width: 0;
    height: 0;
}
```

# checkbox 不与说明文字对齐


```
vertical-align: top || middle;
vertical-align: -3px;
```

# 微软雅黑的英文名字

```
Microsoft Yahei || \5FAE\8F6F\96C5\9ED1
```

# 让IE以最高版本执行

```
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
```
ps:我就觉得这代码没用，有时候用360浏览器打开，IE模式还是quirks呢。。。可能真的有用，写html的时候还是加上吧，就当是心理安慰了。

# Bootstrap

## 使用bs的模态框，点击旁边总是自动关闭

细看文档发现是没修改backdrop属性。

名称 | 类型 | 默认值 | 描述
---|---|---|---
backdrop|boolean 或 字符串 '==**static**==' |true|Includes a modal-backdrop element. Alternatively, specify ==**static**== for a backdrop which doesn't close the modal on click.

修改backdrop属性有两种方法:
1. 在模态框上用data-backdrop来实现

```
<div class="modal fade bs-example-modal-sm" tabindex="-1" role="dialog" aria-labelledby="mySmallModalLabel" aria-hidden="true"  data-backdrop="static">
```
2. 用js控制

```
$('#myModal').modal({
  backdrop: 'static'
})
```

## 使用bootstrap时，写手机页面，页面不缩放，还是1000+px的宽度。
原因：没加 meta viewport

```
 <meta name="viewport" content="width=device-width, initial-scale=1">
```



