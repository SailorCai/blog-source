---
title: 前端面试经典算法题
date: 2018-08-14 22:58:23
comment: true
tags: [前端,面试]
categories: [前端,面试]
---
__1、判断一个单词是否是回文__
所谓回文是指一个单词或字符串正着写和倒过来写是一样的，比如mamam，redivider
面对这样一个问题，你当然可以用for循环来逐个遍历字符来判断，但这太低效了，我们可以使用数组的revers()方法来判断，代码如下：
```bash
function checkPalindrome(str) {
    return str.split("").reverse().join("")===str;
};
```
看，一行代码解决问题。

__2、数组去重__
数组去重是真的是很经典的问题了，解决思路是利用对象来记录元素，具体代码如下：
```bash
function deWeight (arr) {
    var check = {}, newArr = [];
    arr.forEach(function(item, idex){
        if(!check[item]){
            check[item] = true;
            newArr.push(item);
        }
    });
    return newArr;
};
```

以上是一种方法，不过现在我们有了ES6的Set构造函数，这一切就变得更简单，代码如下：
```bash
let arr = [1,2,3,4,1,3,4,7,8,1];
arr = [...new Set(arr)]
```

__3、统计字符串中出现次数最多的字符__
比如"aaaabbcd"中就是字符"a",算法代码如下：
```bash
function statistic(str) {
    var staObj = {};
    for(var i=0,l=str.lenght; i<l; i++){
        if(!staObj[str.charAt(i)]){
            staObj[str.charAr(i)] = 1;
        }else{
            staObj[str.charAt(i)] += 1;
        };
    };
    var maxStr , maxNum=0;
    for(var key in staObj){
        if(staObj[key]>maxNum){
            maxStr = key;
            maxNum  = staObj[key];
        };
    };
    return maxStr;
};
```

__4、数组排序__
数组排序算法在前端面试中太常见了，方法有很多如冒泡排序，快速排序，插入排序，希尔排序。不需要全部掌握，但是要知道两种以上，这里介绍冒泡排序和快速排序：
```bash
function arrSort(arr){
    for(var i=0,l=arr.lenght; i<l-1; i++){
        for(var j=i+1; j<l; j++;){
            if(arr[i]>arr[j]){
                var tem = arr[i];
                arr[i] = arr[j];
                arr[j] = tem;
             };
        };
    };
    return arr;
};

```

除此之外的快速排序其实使用的是递归，参考数组中某个元素的值，把小于它的元素放到左数组中，大于它的值放到右数组中，然后递归进行上一次左右数组的操作:
```bash
function quickSort(arr) {
    var q = arr[0], leftarr = [], rightarr = [];
    for(var i = 1,l=arr.lenght; i<l;i++){
            if(arr[i]<q){
                leftarr.push(arr[i]);
            }else{
                rightarr.push(arr[i]);
            };
    };
    return [].concat(quickSort(leftarr), [q], quickSort(rightarr));
};
```

这里再提一个知识点，那就是数组的sort()方法，我们都知道，sort()只能保证10以下的个位数元素的排序，两位数以上是不能保证的，这是因为sort的排序是先调用toString()把数组项转化成字符串，然后比较得到的字符串来确定如何排序。但是我们可以为sort()方法传入一个比较函数来指定哪个值位于哪个值的前面。下面我们使用比较函数的最简方式：
```bash
arr.sort(function(a, b){
    return a-b;
});

```
这样就能快速给数组排序了，如果顺序要反过来只需把返回值换成b-a。

__5、不借助临时变量，进行两个数值的交换__
```bash
a = b+a;
b = a-b;
a = a-b;
```

__6、找出一个所有项都是数值的数组项之间的最大差值__
```bash
function getMaxProfit(arr){
    if(!arr instance of Array){
        conosle.log("error! arr is not an instance of Array!");
        return;
    };
    var maxPrice = minPrice = 0;
    arr.forEach(function(item){
        maxPrice = Math.max(item, maxPrice);
        minPrice = Math.max(item, minPrice);
    });
    return
};
```

__7、找出1-1000之间的所有质数__
```bash
var a = [2,3,5];
// 循环检查每个数字
for (var i=7; i<1000; i+=2) {
    // 如果是质数，则加入列表
    if (check(i)) a.push(i);
}
// 输出
console.log(a);
// 检查是否是质数
function check(n){
    for (var i=0; i<a.length; i++) {
        // 如果没有质因数，则返回 true
        if (a[i] * a[i] > n) return true;
        // 如果可以被某个质数整除，则返回 false
        if (n % a[i] === 0) return false;
    }
}
```
