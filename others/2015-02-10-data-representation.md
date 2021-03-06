---
layout: post
title: 计算机中数值的表示
category: CS
---

谈谈数值在计算机中的表示。但跳过进制之间的转换。由于水平有限错误在所难免，如有发现恳请指出。


## 1 原码，反码，补码

### 1.1 原码

最高有效位是符号位，用来指明这个数是正还是负。当最高位为1是表明这个数是负数。当为0的时候表示为正数。所以八位二进制数：1000 0011B 的原码表示为 -3。

### 1.2 反码

有的老师说，一个正数的反码表示同它的原码表示，而一个负数的反码表示是其对应整数的原码表示的各位取反。举例：3的反码与原码表示都是 0000 0011B，而-3的反码表示是将-3的原码表示除过符号位各位取反，-3的原码表示为: 1000 0011B ，所以其反码表示为：1111 1100B。这里很容易将符号位与原码表示搞混淆。所以我建议下面这个方法：

我们将最高位解释为负权，其他位为正权。举例：1000 0011B的反码表示为 -1*（2^7-1）+1*(2^6)+1*(2^5)+1*(2^4)+1*(2^3)+1*(2^2)+0*(2^1)+0*(2^0)=-3。 这里最高位的1代表的不是128而是-127，也就是-(2^7-1),如果是32位的数，那么它最高位的1代表的将是-(2^31-1)

### 1.3 补码

看完反码可能你已经晕了，但是没有关系，补码可以将你解救出来。补码的规则很简单，最高位为负权，不像反码那样特殊减去了1，这里如歌是8位的二进制，那么它最高位的权值将是-2^7 也就是 -128，在反码里面最高位权值是 -127。所以我们知道用补码表示的最小的8位二进制数是：1000 0000B = -128 ，而最大为：0111 1111B = 127

这样你就明白了吧。总结一下，原码和反码最小数和最大数的绝对值是相等的，而且0有两种表示方法。对于补码，最小数的绝对值比最大数大1。对于0的表示方法只有一种，那就是全0。以上知识很有用，尤其是补码。所以没有明白的再思考思考。


## 2. 有符号数，无符号数

在计算机中，面对有符号与无符号共存的场景我们常常感到困惑，根本原因在于没有理解他们的表示与两者之间的转换关系。下面分别进行讨论

### 2.1 有符号数

在计算机中，有符号数是用补码表示的，脑补一下补码的表示吧。我们需要最有注意的一个问题是，补码表示的非对称性。

### 2. 2 无符号数

无符号的编码自然不是上面讲的三种编码方式中的任何一种咯。它不会是负数。它的编码方式是最最简单的。1111 1111B=255 ，对就是你想的那样。最高位乖乖地表示128，没有一点问题。

### 2.3 有符号数的上溢和下溢

我们考虑，200*300*400*500对于32位的有符号数来说，它的计算结果，不知道的可能会让他大吃一惊，得到的结果是：-884 901 888，这是一个负数，显然不符合逻辑。原来它发生了上溢，即超过能表示的最大值。同理小于最小的数以后会出现下溢出：

现在将上下溢出的后果总结如下：（皆以8位为例，最大127，最小-128）

+ 上溢：100+30 = 130 上溢，运算结果为 130 - 2^8 = 130 - 256 = -126 。这样你就明白了对8位的数来说，上溢后结果为减去 2的8次方（2^8）。
+ 下移：-100-30 = -130 下溢，结果为 -130 + 2^8 = 126。也就是说发生下溢后结果需要加上一个2的8次方。
+ 没有溢出：100+10 = 110，-100-10 = -110  该是咋就是咋，没有什么特殊的。

对于32位要灵活运用，当一个有符号数大于 2^31-1 后它的结果就是 其减去2^32后的值。

说了这么多，记住这个箭头就是了， 最大值加1会溢出成为最小值，最小值减1就会变成最大值，一个轮回有没有？ -128 <--> 0 <--> 127 <--> -128 <--> ...  所以不要太贪婪，否则前功尽弃啊。这一点在编程中也是最最容易犯错的一定要注意。

### 2.4 无符号数的上溢和下溢

有符号的讲了那么多，为的就是有符号的能少说两句。

...<--> 255 <--> 0 <--> 255 <--> 0 <--> ...      

简单一句话，255加1以后就会变成0，0减去1就会变成255。逆袭啊？

至于公式嘛，就是 大于255的就会减去 2^8（256），小于0的就会加上2^8。


## 3. 有符号无符号之间的转换

有时候，我们在会将一个有符号数赋值给无符号数，有时候反之。会存在什么隐患呢，这样的转换是如何进行的呢。

其实很简单，计算机只是换了一下编码方式而已。有符号数 -1 赋值给无符号数，计算机会将有符号数 -1 的 编码 1111 1111B 按照无符号的编码方式来解读，这样得到的结果就是 255了。

而无符号数 250 赋值给一个有符号数后 250的无符号编码 1111 1010B 会被解读为有符号数，所以结果将是 250-256 = -6. 最高位的权值不再是128了而是-128，所以实际上是被减去了256。

这一点比较容易理解，就是一个编码方式的转变罢了。

## 4. 左移右移

这么简单的东西还拿出来讲，左右移位谁不知道。但是我还是有必要提提。

### 4.1 左移

左移就是各位向左移动一位，之后在最右边置0。所以，左移在不产生溢出的情况下可以看作是乘以了2。

### 4.2 右移

右移比较特殊，对于有符号数由于要保持符号，所以有符号负数右移时在左边补1。如果一直右移的话，最后就就变成0xFFFFFFFF 即-1。对于有符号正数，在其左边补0。也就是用它的符号位来扩展。这样的右移我们称之为算数右移。

对于，无符号数的右移在其左边补0，所以一直右移的话最后会变成0x00000000 即0。这样的右移我们称之为逻辑右移。

总结一下：有符号正数和无符号数在右移的时候都是在左边补0，而有符号负数是在左边补1。

## 5. 浮点数

现在计算机系统中的浮点表示基本都是采用IEEE754表示法。这里对它进行介绍，明白了浮点的表示以后我想你会明白很多之前困扰你的地方。

在C语言里面，我们常常用到float和double这两种浮点类型，当然还有long double。但是他们的原理是一样的。我们介绍32位的float，之后很容易就可以扩展到double，long double。

浮点表示是采用阶码，尾数，符号数这三个部分来表示一个浮点数的。看下图：


![浮点数的字节形式](http://img.blog.csdn.net/20150325225901575)

这个浮点数的值可以表示为 Value=(-1)^S*M*2^(exp-127)，（注意：因为exp表示的是一个无符号数，减去127可以让指数的范围为-127~128）。下面说明这里的S，M，E分别是什么。

用52.1314这个数的存储来说明：

+ 这是个正数所以S为0，S是符号位。
+ 将这个数转换为2进制表示：110100.001000011(转换方法为：整数部分，除2取余，直到商为0，反向取余数。小数部分，乘2，直到被乘数为零，每次乘法的进位顺序取)。
+ 将二进制表示的这个数右移，直到小数点前面只有一位。1.10100001000011，移动了5次。1.10100001000011*2^5 = 110100.001000011
+ 因为小数点前面必然为1，所以在表示的时候可以去掉它。得到基数为：00000000010100001000011 这个是M （小数点后面部分,前面补0，补够23位）
+ 指数实际上是5，所以exp的值为127+5 = 132，exp：10000100


所以52.1314的浮点表示是: 0 10000100 00000000010100001000011

double类型也是一样的，只不过它的exp是11位，fra部分是52位。

其次有下面几个特殊情况需要注意：

+ 所有位为0表示0
+ exp=1111..1(全1) 以及 frac=000..0(全0) 表示无穷大
+ exp=1111..1（全1） 以及 frac!=000..0（不全0） 代表 NAN（not a number）.

你不能理解我也表示理解，这确实需要你好好琢磨琢磨。
