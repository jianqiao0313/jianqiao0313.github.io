---
title: 调试你的nodejs
date: 2020-05-31 21:44:34
tags: nodejs
top_img: https://gezichenshan.top/cms/bg
---

# 背景
最近在研究 **[jscodeshift](https://github.com/facebook/jscodeshift)** ,是一个AST的工具，学习过程中发现调试非常不方便，运行jscodeshift需要以下命令：

```
// --dry指的是不改变源文件，-p指的是打印出输出，方便调试 
jscodeshift -t index.js ./file.js --dry -p
```

这种用cli的命令不像是启动一个express服务器，可以通过node bin/www来启动。直接在index.js里打断点并不起作用。(如下图)

![image](https://static.gezichenshan.top/blog/nodejs/1.png)


# 解决办法一（用vscode 调试 - launch模式）


## 用vscode的debug

```
// launch.json
{
    "version": "0.2.0",
    "configurations": [
        
        {
            "name": "Launch",// 配置名称，将会在启动配置的下拉菜单中显示
            "type": "node",// 配置类型，这里为node
            "request": "launch",// 请求配置类型，可以为launch（启动）或attach（附加）
            "program": "${workspaceRoot}/node_modules/.bin/jscodeshift",// 将要进行调试的程序的路径
            "stopOnEntry": false,// 设为true时程序将暂停在程序入口处
            "args": [ // 运行时候的参数
                "-t",
                "index.js",
                "-d",
                "--run-in-band", // jscodeshift用当前进程进行调试，不加这个参数无法被vscode debug捕获
                "file.js"
            ],
            "cwd": "${workspaceRoot}",// 调试程序时的工作目录
        }
    ]
}
```

在下拉框中运行，即可进入打断点的位置（如下图）

![image](https://static.gezichenshan.top/blog/nodejs/2.png)


# 解决方法二（用chrome调试 - node --inspect-brk）

1. 在package.json中，增加debug命令
```
"scripts": {
    "debug": "node --inspect-brk ./node_modules/jscodeshift/bin/jscodeshift.sh -t index.js ./file.js --dry -p --run-in-band"
  },
```

2. 命令行运行

```
npm run debug
```

3. 控制台输出如下
```
> node --inspect-brk ./node_modules/jscodeshift/bin/jscodeshift.sh -t index.js ./file.js --dry -p --run-in-band

Debugger listening on ws://127.0.0.1:9229/c4ae2261-fe88-40b2-a6cd-c1f182b59c94
For help, see: https://nodejs.org/en/docs/inspector
```

4. 打开chrome，访问 **chrome://inspect/#devices** , 点击 **Open dedicated DevTools for Node** , 如下图

![image](https://static.gezichenshan.top/blog/nodejs/3.png)

5. 此时会自动打开控制台，默认从程序刚开始执行就进行断点（./node_modules/jscodeshift/bin/jscodeshift.sh） 如下图

![image](https://static.gezichenshan.top/blog/nodejs/4.png)

6. 如果不想一步一步跟随程序来进入需要断点的地方，你可以用 **debugger** 命令来打断点，如下图

![image](https://static.gezichenshan.top/blog/nodejs/5.png)

# 总结

以上两种方式可以通过控制台或chrome来进行nodejs程序的调试，比起增加console.log的方式来看方便了不少。后续所有调试nodejs都可按照这两种方式来调试。

有问题欢迎随时留言，或到主页点击右下角的对话icon与我沟通。




