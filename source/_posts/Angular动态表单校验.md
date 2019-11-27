---
title: Angular动态表单校验
date: 2017-10-17 16:17:00
tags: Angular4.0
categories: Angular4.0
---
# 需求
当表单有一个值改变的时候，有一些字段要从不检验变成检验是否输入。

如下图：
![image](https://static.gezichenshan.top/blog/Angular4-4-1.png)
---
![image](https://static.gezichenshan.top/blog/Angular4-4-2.png)
当组织类型为Ordererorg的时候，读权限不需要验证

当组织类型为peerorg的时候，读权限需要验证

# 实现原理
通过自己写校验规则来实现
# 代码

```
ngOnInit() {
    this.validateForm = this.fb.group({
        // 组织类型   必填项
        typeOption: [null, [Validators.required]],
        // 读权限 根据自定义的peerorgValidator来校验
        read: [null, [this.peerorgValidator]],
    });
}
```

```
// type为peerorg时的表单验证函数
peerorgValidator = (control: FormControl): { [s: string]: boolean } => {
    // 如果typeOption是peerorg的话
    if (this.validateForm.controls['typeOption'].value === 'peerorg'){
        // 检查读权限的值，如果为空的话，返回错误{require: true}
        if (!control.value) {
            return { required: true };
        }
    }
}
```

这样就行了嘛？还差点。。。

当typeOption改变的时候，发现read并没有执行校验，需要我们手动执行一下

```
// 定义一个change事件，当typeOption改变的时候 重新校验read
typeOptionChange() {
    setTimeout(_ => {
      this.validateForm.controls['read'].updateValueAndValidity();
    });
  }
  
// html  直接用(ngModelChange)就行
<nz-select formControlName="typeOption"  (ngModelChange)="typeOptionChange()">
        <nz-option *ngFor="let option of typeOptions" [nzLabel]="option.label" [nzValue]="option.value">
        </nz-option>
</nz-select>
```

# OK