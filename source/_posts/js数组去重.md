---
title: js数组去重
tags: []
id: '108'
categories:
  - - 前端开发
date: 2018-09-12 15:25:33
---

### 前言

最近参与公司 ERP 项目, 负责了一下前端页面开发; 之前接触 js 比较浅, 在做项目中遇到了一些问题, 比如数组去重, 对象数组去重等问题;

### 使用 Array.form() set集合

```
let arr = [1,1,2,3,4];
let result= Array.from(new Set(arr));
console.log(result);
}
```

### 遍历,利用 indexOf 判断

```
let arr = [1,1,2,3,4];
var temp= [];
for (i in  arr){
    if(temp..indexOf(arr[i]) == -1){
        temp.push(arr[i]);
    }
}
```

### 遍历 利用对象的 key, 判断数据值

```
var arr = [
    {id: '121', count: 20},
    {id: '122', count: 20},
    {id: '123', count: 20},
    {id: '121', count: 10},
    {id: '122', count: 11}
];
let tempDict = {};
let newArr = [];
   for(let i=0;i<arr.length;i++) {
     if(!tempDict[arr[i].id]) {
       tempDict[arr[i].id] = true;
       newArr.push(arr[i]);
     }
   }
console.log(newArr);
Array(3)
{id: "121", count: 20}
{id: "122", count: 20}
{id: "123", count: 20}
```

### forEach

```
let arr1 = [{skuid:1111,count:222},{skuid:2222,count:3},{skuid:1111,count:222},{skuid:2222,count:3}];
let temp= [];
arr1.forEach(item => {
                    item = Object.assign(item);
                    let sku = this.alertInfoArr.find(sku=>sku.skuId===item.skuId);
                    if(!sku){
                        temp.push(item);
                    }
                });
```