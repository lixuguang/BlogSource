---
title: JavaScript实现经典排序算法
date: 2020-01-04 07:18:22
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# 基本概念
## 时间复杂度
一个算法的时间复杂度反映了程序运行从开始到结束所需要的时间。把算法中基本操作重复执行的次数（频度）作为算法的时间复杂度。
没有循环语句，记作O(1)，也称为常数阶。只有一重循环，则算法的基本操作的执行频度与问题规模n呈线性增大关系，记作O（n），也叫线性阶。
常见的时间复杂度有：

- O(1): Constant Complexity: Constant 常数复杂度
- O(log n): Logarithmic Complexity: 对数复杂度
- O(n): Linear Complexity: 线性时间复杂度
- O(n^2): N square Complexity 平⽅方
- O(n^3): N square Complexity ⽴立⽅方
- O(2^n): Exponential Growth 指数
- O(n!): Factorial 阶乘

## 空间复杂度
一个程序的空间复杂度是指运行完一个程序所需内存的大小。利用程序的空间复杂度，可以对程序的运行所需要的内存多少有个预先估计。

一个程序执行时除了需要存储空间和存储本身所使用的指令、常数、变量和输入数据外，还需要一些对数据进行操作的工作单元和存储一些为现实计算所需信息的辅助空间。

- 稳定
- 不稳定

# 1. 冒泡排序（Bubble Sort） --- 前后两两比较 --- 气泡
冒泡排序可谓是最经典的排序算法了，它是基于比较的排序算法，其优点是实现简单，排序数量较小时性能较好。

它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

## 1. 1 算法原理
相邻的数据进行两两比较，小数放在前面，大数放在后面，如果前面的数据比后面的数据大，就交换这两个数的位置。也可以实现大数放在前面，小数放在后面，如果前面的数据比后面的小，就交换两个的位置。要实现上述规则需要用到两层for循环。

## 1. 2 算法描述
1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
3. 针对所有的元素重复以上的步骤，除了最后一个；
4. 重复步骤1~3，直到排序完成。

## 1. 3 动图演示
![ldB5VS.gif](https://s2.ax1x.com/2020/01/04/ldB5VS.gif)
## 1. 4 js代码实现
```
// js代码

function bubbleSort(arr) {
  var len = arr.length;
  for (var i = 0; i < len; i++) {
    for (var j = 0; j < len - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) { // 相邻元素两两对比
      // 元素交换
      /** 1.使用中间变量 **/ 
        var temp = arr[j + 1]; 
        arr[j + 1] = arr[j]
        arr[j] = temp
        /** 2.适用纯数字的数组排序 **/
        arr[j] = arr[j] +  arr[j + 1]
        arr[j + 1] =  arr[j] - arr[j + 1]
        arr[j] -= arr[j + 1]
        /** 3.使用es6解构赋值 **/
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
      }
    }
  }
  return arr;
}
```
冒泡排序算法优化
```
// js代码

function bubbleSort(arr) {
  var len = arr.length;
  for (var i = 0; i < len; i++) {
    var exchange=false; // 交换标志 
    for (var j = 0; j < len - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) { // 相邻元素两两对比
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]] // 元素交换
        exchange=true; //
      }
    }
    if(!exchange){ // 若本趟排序未发生交换，提前终止算法
        break;
    }
  }
  return arr;
}
```
# 2. 选择排序（Selection Sort） --- 面试挑简历，在剩下的里面挑最好的
表现最稳定的排序算法之一，因为无论什么数据进去都是O(n²)的时间复杂度。。。所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了吧。

## 2. 1 算法原理
先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

## 2. 2 算法描述
n个记录的直接选择排序可经过n-1趟直接选择排序得到有序结果。具体算法描述如下：

1. 初始状态：无序区为R[1..n]，有序区为空；
2. 第i趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n）。该趟排序从当前无序区中
3.  选出关键字最小的记录R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区；
4.  n-1趟结束，数组有序化了。

## 2. 3 动图演示
![ldDWW9.gif](https://s2.ax1x.com/2020/01/04/ldDWW9.gif)
## 2. 4 js代码实现
```
// js代码

function selectionSort(arr) {
    var len = arr.length;
    var minIndex, temp;
    for (var i = 0; i < len - 1; i++) {
        minIndex = i;
        for (var j = i + 1; j < len; j++) {
            if (arr[j] < arr[minIndex]) {     //寻找最小的数
                minIndex = j;                 //将最小数的索引保存
            }
        }
        temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
    return arr;
}
```

# 3. 插入排序（Insertion Sort）----- 麻将/扑克
插入排序（Insertion-Sort）的算法描述是一种简单直观的排序算法。

## 3. 1 算法原理
它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。插入排序在实现上，通常采用in-place排序（即只需用到O(1)的额外空间的排序），因而在从后向前扫描过程中，需要反复把已排序元素逐步向后挪位，为最新元素提供插入空间。

## 3. 2 算法描述
一般来说，插入排序都采用in-place在数组上实现。具体算法描述如下：

1. 从第一个元素开始，该元素可以认为已经被排序；
2. 取出下一个元素，在已经排序的元素序列中从后向前扫描；
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置；
4. 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
5. 将新元素插入到该位置后；
6. 重复步骤2~5。

## 3. 3 动图演示
![ldDfzR.gif](https://s2.ax1x.com/2020/01/04/ldDfzR.gif)
## 3. 4 js代码实现
```
// js代码

function insertSort(arr) {
  // 从1位置开始遍历arr中每元素，同时声明空变量temp
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] < arr[i - 1]) { // 如果当前元素<前一个元素
      let temp = arr[i] // 将当前元素值临时保存在temp中
      let p = i - 1 // 定义变量 p = i- 1
      // 循环 条件：
      // 1. p>=0且temp小于p位置的元素
      while (p >= 0 && temp < arr[p]) {
        // 循环体： 将P位置的值赋值给p的后一个元素
        arr[p + 1] = arr[p]
        p-- // p向前移动一个
      }
      arr[p + 1] = temp // 将temp的值赋值给p+1位置的元素
    }
  }
}
```

# 4. 快速排序（Selection Sort）
快速排序是由东尼·霍尔所发展的一种排序算法。在平均状况下，排序 n 个项目要 Ο(nlogn) 次比较。在最坏状况下则需要 Ο(n2) 次比较，但这种状况并不常见。事实上，快速排序通常明显比其他 Ο(nlogn) 算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。
## 4. 1 算法原理
快速排序又是一种分而治之思想在排序算法上的典型应用。本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法。

## 4. 2 算法描述
选基准：在数据结构中选择一个元素作为基准(pivot
划分区：参照基准元素值的大小，划分无序区，所有小于基准元素的数据放入一个区间，所有大于基准元素的数据放入另一区间，分区操作结束后，基准元素所处的位置就是最终排序后它应该所处的位置
递归：对初次划分出来的两个无序区间，递归调用第 1步和第 2步的算法，直到所有无序区间都只剩下一个元素为止。

简单理解就是，选择一个目标值，比目标值小的放左边，比目标值大的放右边，目标值的位置已排好，将左右两侧再进行快排。
## 4. 3 动图演示
![快速排序.gif](https://i.loli.net/2020/01/04/AtNahbZF9J1dTnS.gif)
## 4. 4 js代码实现
```
// js代码

function quickSort(arr){
    //如果arr.length<=1,则直接返回arr
    if(arr.length<=1){return arr}
    // arr的元素个数/2，再下去整，将值保存在pivotIndex中
    var pivotIndex=Math.floor(arr.length/2);
    // 将arr中pivotIndex位置的元素，保存在变量pivot中
    var pivot=arr[pivotIndex];
    //声明空数组left和right
    var left=[];
    var right=[];
    for(var i=0;i<arr.length;i++){  // 遍历arr中每个元素
        if(i !== pivotIndex){ // 如果i !== pivotIndex
            if(arr[i]<=pivot){ // 如果当前元素值<pivot
                left.push(arr[i]); // 就将当前值压入left
            }else{
                right.push(arr[i]); // 就将当前值压入right
            }
        }
    }
    //递归
    return quickSort(left).concat(pivot, quickSort(right)); // 链接多个数组到 left 从小到大
}
```