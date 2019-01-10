---
title: Angular4.0 两个兄弟组件通过服务通讯
date: 2017-05-10 09:02:22
tags: Angular4.0
categories: Angular4.0
---
# 背景
最近在Taptap上下载一个游戏，叫盗墓长生印，非常有意思，里面有一个小游戏，叫八宝盒，类似于华容道，打算开发一个网页版的自动完成，既然最近在学Angular4.0.0，那就拿Angular4.0开发把。算法我打算用DFS，用Typescript写算法不知道好不好写，二维数组就不怎么好定义。等完成后会放到github上。

网上查了好多，父子组件通讯比较简单，但是兄弟组件通讯只能用service，如果有更好的方法欢迎留言交流。
<!-- more -->
## 附一张八宝盒的截图
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/Angular4-1-1.png)

# 项目结构
![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/Angular4-1-4.png)

## 说明
Main这个组件主要是背景面板，执行DFS算法等。

FooterButton这个组件主要是有开始、重置、上一步、下一步按钮，并且点击八宝盒的滑块可以添加一个八宝盒到Main组件的面板上。

# 问题
如何再单击FooterButton滑块后，告诉Main组件我要添加1号滑块？

# 解决方法
创建一个Service

```
//add-ba-bao-he.service.ts   这是兄弟组件通信的桥梁
import { Injectable } from '@angular/core';
import { Subject }    from 'rxjs/Subject';

@Injectable()
export class AddBaBaoHeService {
  private caseNumber = new Subject<number>();

  // Observable string streams
  caseNumber$ = this.caseNumber.asObservable();

  // Service message commands
  publishData(data: number) {
    this.caseNumber.next(data);
  }

}
//其实还不太懂Subject呢，等有时间再细琢磨下
```

然后就是调用这个服务了

首先是FooterButton调用这个服务，传一个Number类型的值进去。

```
//footerbutton.component.ts  FooterButton组件
import { Component, EventEmitter , Input , Output } from '@angular/core';
import {AddBaBaoHeService} from '../add-ba-bao-he.service';
@Component({
  selector: 'app-footerbutton',
  templateUrl: './footerbutton.component.html',
  styleUrls: ['./footerbutton.component.css'],

})
export class FooterbuttonComponent{
  constructor(private _AddBaBaoHeService : AddBaBaoHeService) {
    this._AddBaBaoHeService.caseNumber$.subscribe(
      data => {
        console.log('Sibling1Component-received from sibling2: ' + data);
      });
  }
  //添加滑块的事件  请看楼下的html代码
  add(type:number){
    this._AddBaBaoHeService.publishData(type);
  }
  ngOnInit() {
  }

}

//footerbutton.component.html
<div class="img_div">
  <img src="../../assets/images/block_sh.jpg"   (click)="add(1)" alt="" class="img">
  <img src="../../assets/images/block_sv.jpg"   (click)="add(2)" alt="" class="img">
  <img src="../../assets/images/block_bh.jpg"   (click)="add(3)" alt="" class="img">
  <img src="../../assets/images/block_bv.jpg"   (click)="add(4)" alt="" class="img">
  <img src="../../assets/images/block_open.jpg" (click)="add(5)" alt="" class="img">
</div>
<div class="button_div">
  <button>开始</button>
  <button>重置</button>
  <button>上一步</button>
  <button>下一步</button>
</div>
```

然后是Main组件接收这个service

```
//main.component.ts Main组件
import { Component, OnInit ,Input , OnChanges , SimpleChanges} from '@angular/core';
import {AddBaBaoHeService} from '../add-ba-bao-he.service';

@Component({
  selector: 'app-main',
  templateUrl: './main.component.html',
  styleUrls: ['./main.component.css'],
})
export class MainComponent implements OnChanges {
  //地图
  map : number[][]=[[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0]];
  imghtml : string = '';
  constructor(private _AddBaBaoHeService : AddBaBaoHeService) {
    this._AddBaBaoHeService.caseNumber$.subscribe(
      data => {
        console.log("滑块是"+data+"号");
      });
  }
  ngOnInit(){

    console.log(this.map);
  }
  //我咋觉得这个函数都进不去呢，不知道是为啥，为什么写这个方法下面解释
  ngOnChanges(changes:SimpleChanges) {
    console.log("changed");
    //console.log(this._AddBaBaoHeService.subscribeData());
    //this.sibling2Form.get('caseNumber').value=this._sharedService.subscribeData();
    //this.searchCaseNumber = this._sharedService.subscribeData();
    //console.log('Sibling2Component-received from sibling1: ' + this.searchCaseNumber);
  }

}

```

最后要在app.module.ts里面 加上AddBaBaoHeService
```
//app.module.ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';
import { FooterbuttonComponent } from './footerbutton/footerbutton.component';
import { MainComponent } from './main/main.component';
import {AddBaBaoHeService} from './add-ba-bao-he.service';

@NgModule({
  declarations: [
    AppComponent,
    FooterbuttonComponent,
    MainComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule
  ],
  providers: [AddBaBaoHeService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

# 运行结果
点击第一个滑块后，Chrome的控制台截图如下

![image](http://gezichenshan.oss-cn-beijing.aliyuncs.com/blog/Angular4-1-5.png)

# 疑惑
main组件里面有个ngOnChanges方法，我觉得并没有执行。这段代码我是从plunker粘过来的，还以为要在ngOnchanges这个函数里面处理逻辑了，原作者的意图好像确实是这样，但不知道为什么就是不执行，Angular4的原理还不太名白，所以暂且放着。

# 结论
完成两个子组件Main和FooterButton的通讯。

# 附参考的plunker

> [Angular2 - Communication between component siblings via a service](https://embed.plnkr.co/P8xCEwSKgcOg07pwDrlO/)
> [网页版八宝盒](http://wmzxtt.duapp.com/babaohe/)（图片是从这个网页上扒的）
