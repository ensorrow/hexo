title: 几种典型算法的js实现
date: 2017-03-11 15:53:17
tags: [算法]
---

前端面试的过程中经常会问到一些比较经典基础的算法问题，今天实践一下。

## 一、数组的典型排序方法

数组的典型排序方法包括选择排序、快速排序、冒泡排序、插入排序，

#### 选择排序

所谓选择排序，思路很简单，就是不断找出第i小的数字。实现方法就是使用两层遍历，外层遍历的值（下标0-length）与内层（外层下标-length）分别作比较，这样就可以实现外层遍历第i次找到的就是第i+1小的实现排序

```javascript
function choiceSort(arr) {
    for(var i=0, total=arr.length;i<total;i++) {
        for(var j=i+1;j<total;j++) {
            if(arr[j]<arr[i]){
                var tmp = arr[i];
                arr[i] = arr[j];
                arr[j] = tmp;
            }
        }
    }
    return arr;
}
```

#### 快速排序

快速排序的思路就是首先选取一个值（一般是第一个），每执行一次函数，就将数组分成大于该值和小于该值的两部分，返回按照这个顺序分好的数组，然后递归调用该函数直到数组不可再分（数组长度小于等于1）

```javascript
function quickSort(arr) {
    if(arr.length <= 1) return arr;

    var some = arr[0];
    var rightArr = [];
    var leftArr = [];
    for(var i=1;i<arr.length;i++){
        if(arr[i]>some){
            rightArr.push(arr[i]);
        } else {
            leftArr.push(arr[i])
        }
    }
    return (Array.prototype.concat(quickSort(leftArr), [some], quickSort(rightArr)));
}
```

#### 冒泡排序

冒泡排序与选择排序很容易混淆，差异就在与它是使用**相邻数字的比较和交换**来找到第i小的数字的，如下

```javascript
function bubbleSort(arr) {
    for(var i=0, total=arr.length;i<total;i++) {
        for(var j=total-1;j>i;j--) {
            if(arr[j]<arr[j-1]){
                var tmp = arr[j-1];
                arr[j-1] = arr[j];
                arr[j] = tmp;
            }
        }
    }
    return arr;
}
```

#### 插入排序

插入排序可能不是很好理解，大概可以描述为以下过程：从数组的第二项开始往后，将当前项与其之前的项比较，找到该项适合的位置插入（实现原理为比该项大的项均往后移，最后赋值到适合的位置）

```javascript
function insertSort(arr) {
    for(var i=1;i<arr.length;i++) {
        var current = arr[i];
        var j = i-1;
        while(current<arr[j] && j>-1) {
            arr[j+1] = arr[j];
            j--;
        }
        arr[j+1] = current;
    }

    return arr;
}
```

## 二、字符串处理相关问题

字符串处理的问题其实本质还是处理数组，换了个形式而已。

#### 实现字符串的reverse方法

```javascript
String.prototype.reverse = function() {
    return this.split('').reverse().join('');
}
```

#### 字符串去重

可以利用对象属性的唯一性进行去重

```javascript
function unique(str) {
    var tmpObj = {};
    var uniqueStr = '';
    str.split('').forEach(function(item) {
        if(!tmpObj[item]) {
            tmpObj[item] = 1;
            uniqueStr += item;
        }
    });
    return uniqueStr;
}
```

#### 随机生成给定长度字符串

生成一个随机数作为下标就行了

```javascript
function randomStr(length) {
    var chars = 'AaBbCcDdEeFfGgHhIiJjKkLlMmNnOoPpQqRrSsTtUuVvWwXxYyZz0123456789';
    var str = '';
    for(var i=0;i<length;i++) {
        var randomIndex = Math.floor(Math.random()*chars.length);
        str += chars.charAt(randomIndex);
    }
    return str;
}
```

前端接触到的算法不多，暂时就这样吧。