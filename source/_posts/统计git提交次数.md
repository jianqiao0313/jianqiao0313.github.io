---
title: 统计git提交次数
date: 2020-06-14 20:10:07
tags: git
categories: git
top_img: https://gezichenshan.top/cms/bg
---

# 背景
之前比较好奇，想看一个仓库所有人的提交次数，修改的行数，如下图（vue仓库）
![image](https://static.gezichenshan.top/blog/git/count/1.png)

输出的格式为：作者邮箱、提交次数、增加的行数、删除的行数、净增加行数

# 步骤

## 1、获取git仓库所有人的提交次数

```
git log --pretty='%ae'| sort | uniq -c | sort -k1 -n -r | head -n 5

```

最后的head -n 5是提取最多的5个，可以根据需要修改。比如你要想统计最多的50个，那么命令就是

```
git log --pretty='%ae'| sort | uniq -c | sort -k1 -n -r | head -n 50
```

命令运行结果如下图
![image](https://static.gezichenshan.top/blog/git/count/2.png)


## 2、获取指定人提交的
```
git log --author="yyx990803@gmail.com" --pretty=tformat: --numstat  | gawk '{ add += $1 ; subs += $2 ; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s",add,subs,loc }' -
```

--author输入咱们之前提取的邮箱即可，如上面所示，填入的是尤雨溪的邮箱

命令运行结果如下图
![image](https://static.gezichenshan.top/blog/git/count/3.png)

## 3、写shell脚本，把第一步和第二步串起来
```
#!/bin/bash
# 最终输出的字符串
outString=""
# 进入vue目录
cd ./vue

# 本年

# 初始化
nameCommmitArr=()
tempNameCommitArr=()
commitArr=()
nameArr=()
countArr=()

# 获取提交次数和用户git id   默认取前50
nameCommmitArr=$(git log --pretty='%ae' | sort | uniq -c | sort -k1 -n -r | head -n 5)
# 打印提交次数和用户git id。例如：2300 yyx990803@gmail.com 70 bot@vuejs.org 47 zhanghan.me@gmail.com 34 defcc@users.noreply.github.com 32 kawakazu80@gmail.com
echo $nameCommmitArr
# 把字符串转换为数组  arr[0]是提交次数   arr[1]是用户git id。
tempNameCommitArr=(${nameCommmitArr//\n\r/})
# 定义一个循环变量i
i=0
# for循环 为的是新建两个数组：提交次数数组和用户git id数组
for var in ${tempNameCommitArr[@]}; do
   temp=$(expr $i % 2)
   if [ $temp == 0 ]; then
      commitArr[${#commitArr[@]}]=$var
   else
      nameArr[${#nameArr[@]}]=$var
   fi
   i=$(expr $i + 1)
done
nameArrLen=${#nameArr[@]}
echo "nameArr数组元素为：${nameArr[@]}"
echo "commitArr数组元素为：${commitArr[@]}"
echo "数组长度为：$nameArrlen"
# 依次查询所有git id提交的记录 返回增加行数,删除行数,净增加行数
for var in ${nameArr[@]}; do
   # 统计数组
   countArr[${#countArr[@]}]=$(git log --author="${var}" --pretty=tformat: --numstat | gawk '{ add += $1 ; subs += $2 ; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s",add,subs,loc }' -)
done

i=0
for var in ${nameArr[@]}; do
   outString="$outString$var ${commitArr[$i]} ${countArr[$i]}\n"
   i=$(expr $i + 1)
done
# 输出
echo $outString
echo $outString > ./count.txt

```
命令运行结果如下图
![image](https://static.gezichenshan.top/blog/git/count/4.png)

# 那如果想把每个月的提交次数都统计出来怎么耍？
## 加个for循环，依次遍历往前的n个月

```
#!/bin/bash
# 最终输出的字符串
outString=""
# 进入vue目录
cd ./vue;

# 本年 往前遍历5个月
for loop in {0..5}
do
   echo $loop
   # 初始化
   nameCommmitArr=();
   tempNameCommitArr=();
   commitArr=();
   nameArr=();
   countArr=();
   # 结束的年份
   endCountYear=`date -d "${loop} month ago" +%Y`;
   # 结束的月份
   endCountMonth=`date -d "${loop} month ago" +%m`;
   # 结束的字符串  形如：2020-05-01 00:00:00
   endDateFormatString="$endCountYear-$endCountMonth-01";
   # 开始的日期应该是结束日期提前一个月 所以tempLoop=loop+1
   tempLoop=`expr $loop + 1`;
   # 开始的年份
   beginCountYear=`date -d "${tempLoop} month ago" +%Y`;
   # 开始的月份
   beginCountMonth=`date -d "${tempLoop} month ago" +%m`;
   # 开始的字符串 形如：2020-04-01 00:00:00
   beginDateFormatString="$beginCountYear-$beginCountMonth-01";
   echo "=== ${beginCountYear}-${beginCountMonth} to ${endCountYear}-${endCountMonth} ==="
   outString="$outString=== ${beginCountYear}-${beginCountMonth} to ${endCountYear}-${endCountMonth} ===\n";

   # 获取提交次数和用户git id   默认取前50
   nameCommmitArr=`git log --pretty='%ae' --since=$beginDateFormatString --until=$endDateFormatString | sort | uniq -c | sort -k1 -n -r | head -n 50`;
   # 打印提交次数和用户git id。例如：1390 liuyong 896 zhangjiele
   echo $nameCommmitArr;
   # 把字符串转换为数组  arr[0]是提交次数   arr[1]是用户git id。
   tempNameCommitArr=(${nameCommmitArr//\n\r/})  
   # 定义一个循环变量i
   i=0;
   # for循环 为的是新建两个数组：提交次数数组和用户git id数组
   for var in ${tempNameCommitArr[@]}
   do
      temp=`expr $i % 2`;
      if [ $temp == 0 ]
      then 
         commitArr[${#commitArr[@]}]=$var
      else
         nameArr[${#nameArr[@]}]=$var
      fi
      i=`expr $i + 1`;
   done 
   nameArrLen=${#nameArr[@]};
   echo "nameArr数组元素为：${nameArr[@]}";
   echo "commitArr数组元素为：${commitArr[@]}";
   echo "数组长度为：$nameArrlen";
   # 依次查询所有git id提交的记录 返回增加行数,删除行数,净增加行数
   for var in ${nameArr[@]}
   do
      # 统计数组
      countArr[${#countArr[@]}]=`git log --author="${var}" --pretty=tformat: --numstat --since=$beginDateFormatString --until=$endDateFormatString | gawk '{ add += $1 ; subs += $2 ; loc += $1 - $2 } END { printf "%s,%s,%s",add,subs,loc }' -`
   done

   i=0;
   for var in ${nameArr[@]}
   do
      outString="$outString$var ${commitArr[$i]} ${countArr[$i]}\n";
      i=`expr $i + 1`;
   done
done
echo $outString;
echo -e $outString > ./gitCount.txt
```

命令运行结果如下图(gitCount.txt文件)
![image](https://static.gezichenshan.top/blog/git/count/5.png)

# 拿到上面的统计结果，想放入excel里查看怎么做？

## 安装node-xlsl库
```
npm init
npm i node-xlsx
```

package.json如下：
```
{
  "name": "count",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "node-xlsx": "^0.15.0"
  }
}

```

index.js如下
```
const fs = require('fs');
const readline = require('readline');
const xlsx = require('node-xlsx');
const outObj = {};
let outObjKey = "";
let _this = this;

function readFileToArr(fReadName, callback) {
    var fRead = fs.createReadStream(fReadName);
    var objReadline = readline.createInterface({
        input: fRead
    });
    objReadline.on('line', function (line) {
        if (line.indexOf('===') != -1) {
            titleHandle(line);
        } else {
            dataHandle(line);
        }
    });
    objReadline.on('close', function () {
        callback();
    });
}

function titleHandle(line) {
    let dateString = line;
    let beginDate = line.split(" ")[1];
    let endDate = line.split(" ")[3];
    outObj[`${beginDate} - ${endDate}`] = {};
    outObjKey = `${beginDate} - ${endDate}`;
}

function dataHandle(line) {
    if(line === "") return;
    let splitArr = line.split(" ");
    let name = splitArr[0];
    let commitNum = +splitArr[1];
    let countArr = splitArr[2].split(",");
    let addNum = +countArr[0] || 0;
    let removeNum = +countArr[1] || 0;
    let pureAddNum = +countArr[2] || 0;
    if (outObj[outObjKey][name]) {
        outObj[outObjKey][name]['commitNum'] += commitNum;
        outObj[outObjKey][name]['addNum'] += addNum;
        outObj[outObjKey][name]['removeNum'] += removeNum;
        outObj[outObjKey][name]['pureAddNum'] += pureAddNum;
    } else {
        outObj[outObjKey][name] = {
            commitNum,
            addNum,
            removeNum,
            pureAddNum
        }
    }

}

readFileToArr('./gitCount.txt', buildExcel);

function buildExcel() {
    let excelObj = [];
    for (let date in outObj) {
        let sheetName = date;
        let excelDate = [];
        let title = ['Git ID', '提交次数', '增加行数', '删除行数', '净增加行数'];
        excelDate.push(title);
        for (let item in outObj[date]) {
            excelDate.push([item, outObj[date][item]['commitNum'], outObj[date][item]['addNum'], outObj[date][item]['removeNum'], outObj[date][item]['pureAddNum'], ])
        }
        excelObj.push({
            name: sheetName,
            data: excelDate
        })
    }
    execExcel(excelObj);
}

function execExcel(excelObj) {
    let buffer = xlsx.build(excelObj);
    fs.writeFileSync('./git统计.xlsx', buffer, {
        'flag': 'w'
    }); //生成excel the_content是excel的名字，大家可以随意命名

}
```

## 运行命令后就会生成excel文件了
```
node index.js
```

最终生成的excel文件如下图：
![image](https://static.gezichenshan.top/blog/git/count/6.png)