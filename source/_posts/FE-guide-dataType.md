---
title: 前端常见知识点整理 ---- 数据类型、类型判断、类型转换、类型比较
date: 2020-01-02 16:22:22
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# 前言

> 温故而知新，可以为师矣。   
>            ———————— 论语

这篇文章主要是把我看过的书中的知识点，还有其他人整理的面试题进行整理，巩固自己的前端开发理论知识，希望其他看到这篇文章的人也能有所帮助。
<!-- more -->

# JS知识点
> 当前市场中，如何区分一个好一点的前端开发和一般的前端开发，主要看的就是js能力的差距，好的前端开发，JS玩的转，不仅仅是框架玩的好，还要JS的基础扎实，只有基础与新技术都玩的6的前端开发才是好前端。

上面这句话不是什么名人或者某位知名前端大拿说的，这是我在公司这几年面试招聘的过程中真真切切总结感受的。
所以可以说得JS者得高级前端，所以下面也主要是写JS的相关知识点。

## JavaScript内置数据类型 --- 数据类型
无论学什么语言最重要最基础的就是数据类型，《JavaScript权威指南》这本书中详细的介绍了JS中的数据类型，下面总结一下。

JavaScript中数据类型分为两大类共七种内置数据类型，其一是6种***`基本类型`***，其二是1种***`引用类型`***，我发现在面试过程中好多面试者都不会先说有两大类，而是会直接蹦出数字类型、字符串类型。。。对象、数组也会被并排放在一起，这其实不是个掌握知识点的好方法，应该先把数据类型分成上面说的***`基本类型`***、***`引用类型`***这样两个大类之后，再看看这两大类中有什么其他的子类型，在记忆其他子类型之前我觉得应该先了解一下什么是***`基本类型`***和***`引用类型`***，实际上基本类型和引用类型的主要区别是存储的区别，基本类型在栈中，而引用类型的话，引用数据的地址存储在栈中，而对象本身是存储在堆中，引用的数据地址是个16进制的数据值，它就像一把钥匙让你能够找到宝藏在什么地方。这就是基本数据类型和引用类型的区别了。

那么如何记住有哪些基本数据类型和引用数据类型呢，实际上只要记住了6个基本数据类型，其他的都是引用数据类型，而所有的引用数据类型的祖宗都是Object，所以引用数据类型实际上只有Object一个，那么像是Array等其他子类型，都是Object的孩子，不跟Object在一个级别上。

基本数据类型有哪些呢？其实挺好记的，数字，字符串，这两个一个像温柔的文学少女（string），一个像有点精于算计的男生（number），还有一个布尔类型（boolen）他像是班级里正义感爆棚的人，只论对错；另外还有个差生，没头脑似的未定义（Undefined）还有一个失了忆记不起来自己是谁的空（Null），最后还有一个新加入的插班生，总是带着口罩的标志符号（Symbol），这些人构成了这个班级的所有学生，也就是全部的基本类型，那么引用类型的对象Object呢就像及了漂亮爱化妆的班主任老师，有好多副面孔。不知道大家有没有看过一个动漫叫做《黑塔利亚》，他就是把国家都拟人化了，有了各自的性格，我很喜欢看，我觉得这些数据类型也各有各的特点，像这些国家一样，好了脑洞有点挖深了，有人会说我不就是这么几个简单的数据类型嘛，硬记下来不就好了，但是知识总有你硬记不下来的时候，最好的方法也是速记领域最为常用的方法，就是把你不熟悉的知识与你感兴趣的画面或者既往的知识串联起来，这样就能达到很好的记忆，如果你不喜欢动漫（怎么会有不喜欢动漫的人！！！），可以试试用其他的方法记，当然你如果硬要死记硬背那我也没办法，我继续开我的脑洞。
如果你是学过Java开发的同学（如果是计算机专业出来的，应该都或多或少学过，非计算机专业的我也不知道说啥了。。），数字在Java中是分成 byte/short/int/long 的，但是在Js中没有那么多，就一个Number，它是浮点类型，基于 IEEE 754标准实现，刚才我不是说了Number是个精于算计的男生，精于算计就是说他分毫不差，这样浮点型就很好的记了下来，这个754的标准可以不记，如果非要记的话，你可以记成他是自称IEEE 754团体的成员。最后Number身后还跟着一个小弟，叫做NaN，他虽然是Number的小弟，但他总是说话不算数，自己说过什么都不承认。所以NaN!=NaN。
老师是个爱化妆的老师，而这些学生也不是普通的学生，在学校他们是老老实实的基本类型，放了学之后一打扮，他们就各有了其他的能力，这个过程叫做装箱，具体的后面再说。（好了快回到现实吧你！）

如果基础数据是字面量类型，那么他们就像是在上课的学生，只是学生而已，而当他们调用方法时，他们就成了下课后各种技能都有的新新人类，这个过程有时候是显示的，就像是有些学生喜欢大声嚷嚷，而更多的是你不自觉中就用到了装箱操作，是Js引擎提供的能力，就像是有些闷骚的学生一样。
```
// js代码
let aNumber = 111 // 这只是字面量，不是 number 类型
aNumber.toString() // 装箱操作自动转化成数字对象，使用时候才会转换为对象类型
```
对象有个深浅拷贝的知识点必须要会，对象因为是引用数据类型，在栈中存储的是地址，当用另一个变量接收了之前的变量，那么就好像把钥匙复制了一把，两把钥匙开的还是同一个门，而深拷贝呢就像是照着原来的样板间又造了一个一模一样的房间，这两个房间长得一样，但就是两个房间，钥匙自然也是不一样的，所以呢，当往房间里搬家具的时候，浅拷贝搬进去的是一个房间，所以两把钥匙打开之后看到的都是多了家具，而深拷贝的话，我只是往样板间搬了家具，所以照着装修的房间里是不可能有的，这就是浅拷贝原数据会受影响，而深拷贝不会。
```
// js代码

let a = { name: 'FE' }
let b = a
b.name = 'EF'
console.log(a.name) // EF 浅拷贝原数据会受影响
```
## 内置数据类型检测 Typeof --- 类型判断
> typeof 对于基本类型，除了 null 都可以显示正确的类型

typeof 就像是学校的教导主任一样，他有着一双火眼金睛，不管是哪个同学，穿了什么样的衣服，他一问就能问出来这个学生是谁，大家都怕他，但是Null因为失忆了，他也不知道教导主任是谁，所以typeof就拿他也没办法，因为他不怕教导主任，教导主任甚至会以为他是老师呢。

```
// js代码

typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof b // b 没有声明，但是还会显示 undefined

typeof null // 'object' 这是JS中的bug
```

> typeof 对于对象，除了函数都会显示 object

```
// js代码

typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'
```

> 知识扩展：为什么会出现这种情况呢？因为在 JS 的最初版本中，使用的是 32 位系统，为了性能考虑使用低位存储了变量的类型信息，000 开头代表是对象，然而 null 表示为全零，所以将它错误的判断为 object 。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。

***如果我们想获得一个变量的正确类型，可以通过 Object.prototype.toString.call(xx)。这样我们就可以获得类似 [object Type] 的字符串。***

> 小知识扩展
```
let a
// 我们也可以这样判断 undefined
a === undefined
// 但是 undefined 不是保留字，能够在低版本浏览器被赋值
let undefined = 1
// 这样判断就会出错
// 所以可以用下面的方式来判断，并且代码量更少
// 因为 void 后面随便跟上一个组成表达式
// 返回就是 undefined
a === void 0
```

## 类型转换
### 转Boolean
一句话可以概括
> 在条件判断时，除了 `undefined`， `null`， `false`， `NaN`， `''`， `0`， `-0`，其他所有值都转为 true，包括所有对象。

从上面这段话可以看出来，`undefined`、`null` 是基本类型之二，而`false`是布尔类型的假值，`NaN`是数字类型的无效值，`''`是字符串类型的空值，而`0`， `-0`都是数字类型的零值，可以看到，除了`0`、`-0`有些特殊，除了插班生`Symbol`，剩下的都是基本类型的假值，由此实际上就很好记了，有时候数字这个容易忘，但是记住“非0既真”这句话就好了。

### 对象转基本类型
对象在转换基本类型时，首先是先会调用ToPrimitive（原始类型），如果有hint参数调用对应的的类型方法，如果没有那默认先会调用 valueOf 然后调用 toString。如果返回了基本类型，结束。如果都没返回，那么Error***但是注意这两个方法你是可以重写的。***

> Symbol.toPrimitive 是一个内置的 Symbol 值，它是作为对象的函数值属性存在的，当一个对象转换为对应的原始值时，会调用此函数。（来自MDN的解释）
> 需要解释的关键机制是ToPrimitive函数。该函数是将任意值转换为相应的基本类型值。如果输入的就是一个基本类型值，那么将不做修改，被直接返回。如果值是非基本类型值，它将调用内部方法 [[DefaultValue]] 为对象找到一个默认值。
> [[DefaultValue]]是每一个对象的内部属性。该方法需要一个可选的参数hint，值是Number或String。如果没有提供hint，则默认为Number(除非该对象是Date，在这种情况下默认为String)。然后将调用toString和valueOf去寻找基本类型值。在这里hint就起到作用了。如果hint参数值为Number，valueOf将先被调用，如果hint是String的话，则toString被先调用。
> [[DefaultValue]] 返回的值一定是基本类型值。如果不是，一个TypeError 将会被抛出。这就意味着为了在这种情况下有意义，toString和valueOf应该返回基本类型值。

### 四则运算符
> 只有当***加法***运算时，其中一方是***字符串类型***，就会把另一个也转为字符串类型。其他运算只要其中一方是数字，那么另一方就转为数字。***并且加法运算会触发三种类型转换：将值转换为原始值，转换为数字，转换为字符串。***

```
// js代码

1 + '1' // '11' 这里会是面试陷阱
2 * '2' // 4 
[1, 2] + [2, 1] // '1,22,1'
// [1, 2].toString() -> '1,2'
// [2, 1].toString() -> '2,1'
// '1,2' + '2,1' = '1,22,1'
```
***面试陷阱题之 ++ ***
```
// 问表达式 'a' + + 'b' 返回结果是什么？
// 答案是 'aNaN'
'a' + + 'b' -> // 一元运算符优先级高
'a' + (+ 'b') -> // +'b'转数字类型，非有效结果是NaN
'a' + NaN.toString() -> // NaN调用toString（）成字符串'NaN'
'aNaN' // 字符串接到一起后'aNaN'

// 类似题 '1' + + '4' 返回结果是什么 
// 其实就是'4' -> 4 -> '4' 最后还是'14'
```

## == 操作符 --- 类型比较
> 相等运算符的运算规则如下：
1、如果两个值类型相同，进行 === 比较。(这个非常好理解，就不多说了)
（1）数字比大小
（2）字符串就通过 unicode 字符索引来比较
2、如果两个值类型不同，他们可能相等。根据下面规则进行类型转换再比较：
（1）如果一个是null、一个是undefined，那么[相等]。 // 这个有点特殊需要单独记
（2）如果任一值是字符串，另一个值是数值，在比较相等性之前先将字符串转换为数值；即是调用Number()函数。
（3）如果任一值时布尔值，则在比较相等性之前先将其转换为数值，即是调用Number()函数。
（4）如果一个是对象，另一个是数值或字符串，把对象转换成基础类型的值再比较。对象转换成基础类型，利用它的toString或者valueOf方法。 （js核心内置类ToPrimitive，会尝试valueOf先于toString；例外的是Date，Date利用的是toString转换。）

1不说，2（1）的话单独记，其他基本类型转数字比较，引用性数据类型调用ToPrimitive转换成基本数据类型

首先还是一道面试题
```
// [] == ![] 结果是什么
[] == ![] // true
```
为什么呢？我们来解析一下，
1. 首先我们看一下右侧，`![]`肯定是要先转换成boolen类型了吧，那么`[]`的布尔类型是什么呢，上面转换布尔的时候我们说过，除了那五个基本类型的假值以及正负0之外，都是真值，所以`[]` -> `true` `![]` -> `false`
2. `ToPrimitive(false)`->`0`右边的值得出了数值类型的原始值
3. 看左边`ToPrimitive([])`->`[]`.toString()->`''`
4. `Number('')`->`0`
5. 比较左右 `0 == 0` -> `true`

# 最后
到这里JS的内置数据类型及类型的转换和比较就讲完了，相信大家看过以后一定会记得住的
PS：突然好像学画漫画，《JS数据结构们》，一定大火，哈哈哈😂