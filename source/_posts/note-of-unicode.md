---
title: 浅谈unicode
date: 2020-07-13 21:47:15
tags:
    - unicode
updated:
categories:
    - 技术
keywords:
description:
top_img:
comments:
copyright: true
---

### 基础知识

一个血淋淋的现实是：计算机只能处理数字。（计算机的CPU和内存只能处理二进制形式的数字）

在计算机被发明的初期，伟大的科学家们就是用二进制和计算机交流的。随着输出设备（例如屏幕/打印机）的出现，就产生了这么一种需求：希望输出设备能够输出人类能够轻松阅读的字符。

在继续讨论之前，先捋一捋情况：

1. 计算机只能处理数字；
2. 我们人类希望计算机处理字符；

怎么解决这个问题呢？在揭开谜底之前我想说一下一个关于我的故事：

> 我读初中的时候学校每月都会大考试一次（月考），我记得有一次是考的英语，我身边一哥们被老师当场截获用来传递答案的纸条。那时我撇了一眼那个纸条，发现上面写满了“ABAAC DDDCA ...”等英语选择题的答案。那时候我脑海里就冒出这样一个念头：“为什么他们直接把答案写在纸上呢？这样一来只要被老师发现纸条，作弊罪名立马成立，连反驳的机会都没有。”情不自禁地我脑海里就冒出了这样一种思路：我用数字1代表字符`A`， 数字2代表字符`B`，数字3代表字符`C`， 数字4代表字符`D`。这样如果我想传递“ABAAC DDDCA ...”， 那么我就在纸上写“12113 44431 ...”。这样一来，哪怕纸条被老师截获，至少不会马上被“判刑”。（PS：后来我又在此基础想了好几种升级方案，与本文无关就不说了）

上面有一个很重要的思路：**我们可以约定用某个数字表示某个字符。**也就是说，我们可以通过这个方法让计算机**间接**处理字符。

### ASCII

在前面我们其实是得到了一种**映射关系**：`数字` <------> `字符`。（划重点：记住映射关系是什么）

将这种思路发扬光大的是ASCII（American Standard Code for Information Interchange），它约定数字65映射到字符`A`, 数字66映射到字符`B`..., 这些一个个的映射关系形成了一个集合，如下（这就是大名鼎鼎的ASCII码表）：

![](https://codepool.oss-cn-shenzhen.aliyuncs.com/unicodeNote/unicode00.gif)

别小看这张表，这张表里面的字符其实已经解决了当时人们对计算机的输出需求。

---

我必须强调，上面那张表其实只是定义了一种映射关系，这个映射关系是抽象的。因此如果我们想要在计算机中实现这张表中抽象的映射关系，就涉及到了**编码/解码**。

关键名词几乎都出来了：映射关系，编码/解码。理解这几个名词之间的关系实在太重要了：

> **映射关系只是定义了某种映射规则（例如65 <------> `A`），对计算机来说，映射关系是抽象的东西，是“虚无缥缈”但计算机之间又必须遵守的规则。编码/解码就是计算机用实际行动去遵守映射关系的方式。**

---
到了这里，有些东西应该是清晰的：

> 我们目前讨论的ASCII码只需要7个bit就可以完全表示（懂二进制的人都知道7个bit就能表示0～127之间的数字）。

现在要考虑的问题是：如果要在内存中表示某个ASCII码表中的数字，需要多少个bit？答案是：大于或等于7个bit即可。

所以，假如我们要在内存表示65，有很多"编排"方式（如果内存足够大，其实是无数种）：

1.  `100 0001`：占用7个bit；
2. `0100 0001`：占用8个bit，1个字节；
3. `0000 0000 0100 0001`：占用16个bit，2个字节；
4. `0000 0000 0000 0000 0000 0000 0100 0001`：占用32个bit，4个字节；
......



`100 0001`,`0100 0001`等这些二进制组合就是字节序列。**将映射关系中的数字变成字节序列的过程就是编码。将字节序列变成映射关系中的数字的过程就是解码。**

在历史选择和那时的硬件发展水平之下，一个字节=8个位（8-bits=1byte），因此，上面说的第二种编码方式（65 ----> `0100 0001`）名正言顺成为ASCII码的正式编码方式，这种编码方式的特点就是每次编码8个bits（也就是所谓的定长编码），我们一般直接将这种编码/解码方式称之为`ASCII编码`。洋洋洒洒说了这么多，无非就是下图的几点东西：

![](https://codepool.oss-cn-shenzhen.aliyuncs.com/unicodeNote/unicode01.png)

### ANSI标准

我们可以列出一些已知条件：

- ASCII字符集只需要7个bit；
- ASCII编码是以8-bits为单位去编码的；
- 二进制的7个位表示的范围是：000 0000～111 1111；换成十进制是：0～127
- 二进制的8个位表示的范围是：0000 0000～1111 1111；换成十进制是：0～255；

我觉得有些东西已经很明显了：**使用ASCII编码去编码ASCII字符集的时候，是有一个bit空闲下来的，这个空闲位使得我们拥有128～255的空间**。聪明的人肯定不止一个，那时候就有一批人盯上了这个空闲位，他们各自对从128到255的空间应该如何使用有自己的想法：

> IBM-PC有一种被称为OEM字符集的东西，它提供了一些针对欧洲语言的重音符号和一堆线描字符（各种款识的水平条，垂直条），然后您可以使用这些线条绘制字符在屏幕上制作漂亮的框和线条。实际在计算机商业化的初期，人们在美国以外的地方购买PC时，就构想了各种用途的类OEM字符集，它们都将128～255这个空间段用于自己的目的。例如，在某些PC上，字符代码130将显示为é，但是在以色列出售的计算机上，该字符是希伯来字母Gimel（ג），在很多情况下，不同厂商对大于128对应的字符的处理方法有很多不同的做法。

为了保证计算机始终能够显示英语，ANSI标准出来了：在ANSI标准里，规定0～127表示的就是ASCII字符集里面的内容，但是对于**128以及128以上**的数字所映射的字符，取决于你的国家（这里有些东西已经悄然变化，从`128～255`变成了`128以及128以上`,最明显的原因就是有些国家的文字需要用两个字节才能表示）。

因此就形成了大量的编码系统（编码系统被称为code pages）。这些编码系统有一个共同的特点：**它们都能正确显示英文（即兼容ASCII）和本地文字**。

只能够处理英语和本地文字肯定是不够的，因此随后各个标准和升级版编码系统不断出台，为的无非就是一件事：让自家的电脑能够尽量显示更多文字。

各个国家都有各自的编码系统，大家你一套我一套的，不互相交流还好，随着国际化大家交流越来越密切，这时候问题的严峻已经显露出来：各个国家之间的编码系统不互相兼容。

因此，unicode顺势而来！

### Unicode

Unicode其实就是为了打破那时编码系统的混乱，从而在全球范围内实现一个统一的编码系统。简单来说就是：

![](https://codepool.oss-cn-shenzhen.aliyuncs.com/unicodeNote/unicode02.jpeg)

---

定制Unicode标准之初，人们觉得用16个bits（范围是0～65536）就可以将全球的文字符号都包括进去了（16个bit刚好两个位）,而且还给未来拓展留下了空间，Unicode 1.0发布的时候才用了不到0～65536数值范围内的一半数字。但很快Unicode就超过了65536个字符。主要原因是中日韩的文字(ideographs:表意文字)太多（不断地被发现）。参考来源Core Java：

> When the unification effort started in the1980s, a fixed 2-byte code was more than sufficient to encode all characters used in all languages in the world, with room to spare for future expansion—or so everyone thought atthe time. In 1991, Unicode 1.0 was released, using slightly less than half of the available 65,536 code values.Unicode grew beyond 65,536 characters, primarily due to the addition of a very large set of ideographs used for Chinese, Japanese, and Korean. 

目前，Unicode使用21个bits，范围是0到0x10FFFF（大约110万个值，到目前为止分配了11万个）。如今Unicode字符集里已经包含了很多字符，除了各国的文字，还包括了各种表情（😊😢）。有兴趣的可以去[Unicode首页](https://home.unicode.org/)看看。

**划重点：Unicode原来占用16个bits（2个字节），现在占用21个bits（不到四个字节）**

---

因此，我们简单粗暴地先认识一些重要的概念：

- unicode代码空间（the Unicode codespace）：Unicode标准里面用21个bits表示的空间范围。范围是：0～0x10FFFF。
- 字符集（Character Set）： 一个字符集，这个字符集里面的每个字符都被赋予一个数值型的码点（code point）。又被称为Coded character set, charset,或者code set。
- 字符（character）：字符是文本的最小组成部分。例如`A`, `B`, `C`...就是不同的字符。同理，`中`和`花`也是两个不同的字符。但有时候，字符的含义是和你所处的环境有关的。比如“罗马数字1:`Ⅰ`”就和小写字母`I`长得一样，但其实它们是两个不同的字符，它们所表示的含义不一样。
- 码点（code point）：（1）字符集里每个字符都对应一个数值，这个数值就是码点。（2）unicode代码空间（the Unicode codespace）里面的任何值都是码点,范围是0～0x10FFFF。不是所有的码点都有对应的字符。

![](https://codepool.oss-cn-shenzhen.aliyuncs.com/unicodeNote/unicode03.png)

Unicode标准描述了Character到code point之间的一种映射关系。Unicode标准中包含了一系列character，而且每一个character都有唯一的code point（ps：code point被写成U+1D17这样的格式，是表示这个字符对应的值是0x1d17）：

![](https://codepool.oss-cn-shenzhen.aliyuncs.com/unicodeNote/unicode04.png)

在非正式的语境之中，我们可以将code point和character等同起来。字符在屏幕或纸上由一组称为字形的图形元素表示。 例如，大写字母A的字形是两个对角线笔触和一个水平笔触，尽管确切的细节将取决于所使用的字体。 找出要显示的正确字形通常是GUI工具包或终端的字体渲染器的工作。

### 编码

对前面内容的一个小总结：

> Unicode String是一段序列化的code points。（code point的范围是0～0xFFFF，十进制是0～1,114,111）。这个code points序列在内存里表现成一组code units，最后这组code units会在内存中被映射成一个个字节，这一个个字节就构成了字节序列。将Unicode String“翻译”成字节序列的规则就是字符编码，简称编码。

在ASCII码里，通用的编码方式只有一种：以8-bits一组（即以8-bits为一个code unit）将数字转化成字节序列，这就是我们所说的ASCII编码（注意！注意！注意！字节序列是类似于0101011的东西。字节序列不是不是不是字符！！！）

在Unicode标准里，通用的编码当时有很多种（UTF-8，UTF-16，UTF-32）。没有`Unicode编码`这个名词。

**！！！注意：code point和code unit是不同的。**

### UFT-32

有了前面ASCII的历史经验，我们很容易想到的是：以32-bits作为一个`code unit`将Unicode String转化成字节序列，这就是UTF-32（ps：这里的Unicode String就是序列化的code points）。如果用这种方式表示我们的字符串"python"：

```python
>>> "python".encode("utf32")
b'\xff\xfe\x00\x00p\x00\x00\x00y\x00\x00\x00t\x00\x00\x00h\x00\x00\x00o\x00\x00\x00n\x00\x00\x00'
>>> len("python".encode("utf32"))
28
```

如果是原先的ASCII编码方式，如下：

```python
>>> "python".encode("ascii")
b'python'
>>> len("python".encode("ascii"))
6
```

很明显，如果采用UTF-32这种编码方式，我们需要花费原先ASCII编码的4倍左右的内存空间。

---

先列出一些我们的已知情况：

- Unicode字符集只需要21个bit；
- UTF-32编码是以32-bits为code unit去编码的；
- 二进制的21个位表示的范围是：0 0000 0000 0000 0000 0000～1 1111 1111 1111 1111 1111；
- 二进制的32个位表示的范围是：0000 0000 0000 0000 0000 0000 0000 0000～1111 1111 1111 1111 1111 1111 1111 1111。

咱直接上结论：**因为UTF-32编码能力是32位，因此UTF-32完全有能力编码21位的Unicode字符集，不需要额外的转换，所以UTF-32编码方式编码速度快。**

---

到了这里，UTF-32的最明显的优缺点都出来了：

- 优点：速度快(ps:当然还有其他优点)；
- 缺点：浪费空间。比如编译字符`P` ------> `p\x00\x00\x00`。里面有3个字节都填充了0。目前计算机处理的大多数还是英文字符，因此白白浪费了很多空间。除此之外还有一堆缺点，比如不可兼容以前的8-bits机器；

### UTF-8

如果只是在内存里花费4倍的空间还是可以接受的。但如果是存在硬盘或者是在网络上传输，4倍的存储/传输成本令人抓狂。（这里的4倍其实是最坏情况）


针对目前计算机需要处理的大多数还是英文字符，UTF-8来了。UTF-8有下面这些方便的属性：

1. 它能够处理所有的Unicode code point（它以8-bits作为code unit，却有能力编码最大21-bits的unicode code point本身就是一种超强能力）；
2. 它的编码是变长的。对于原先ASCII字符集里面的字符，它只需要用8-bits。（ps：是不是和原先的ASCII编码一样？这就是真正的兼容ASCII，哪怕你把ASCII年代的程序拿过来，UTF-8还是能够正确读取源程序）对于中文，它则使用3个字节去编码。
3. 容错能力强悍。哪怕字节序列损坏，它也能够跳过损坏部分，进而正确解码后面的字符。
4. 对于英文字符为主的文本，几乎可以这么认为：UTF-8占用的空间是UTF-32的1/4。
5. 当然，它的缺点也明显：`21-bits的code point`与`8-bits为一个code unit的字节序列`之间的转换需要更多系统开销。编码速度肯定比UTF-32慢。

除此之外，还有一堆编码方式，感兴趣的话请看：[Mapping_and_encodings](https://en.wikipedia.org/wiki/Unicode#Mapping_and_encodings)。

### Unicode和ASCII之间的一些我们需要注意的区别

最主要的区别是：

1. ASCII编码是一种编码方式；
2. Unicode编码是一堆编码方式的集合。所以你说Unicode编码我会很疑惑你到底是在说哪个编码。我甚至在Unicode官网都找不到Unicode编码这个专有名词。

![](https://codepool.oss-cn-shenzhen.aliyuncs.com/unicodeNote/unicode05.png)

### 一些十分有用的FAQ

下面的内容部分来自于[Unicode官方FAQ](https://www.unicode.org/faq/utf_bom.html)，强烈推荐各位同学去阅读！

Q：Unicode是16位编码吗？（Is Unicode a 16-bit encoding?）

> 不是。第一个版本的Unicode使用的是16位编码（1991～1995年）。但从Unicode 2.0开始（1996年7月）它就使用21位作为字符集的空间范围（范围是：0～0x10FFFF）。根据您所选的编码方式的不同，Unicode字符需要的字节数是不同的。

---

Q：Unicode文本可以用多种方式（的字节序列）表示？（Can Unicode text be represented in more than one way?）

> 嗯。肯定可以的，编码方式不一样，字节序列就可能不一样。

---

Q：在计算机内存里，单个Unicode字符占用多少个bits？

> 在Unicode标准里面，单个字符是需要21-bits来表示的。**但是这篇文章通篇都在强调，Unicode字符集里面的字符其实是一种抽象的东西，它是不直接在内存里面表示出来的。如果需要在内存里表示出来，就必须经过编码。**因此在计算机的内存里单个Unicode占用多少个字节，**完全取决于系统采用的编码方式**。比如Window 10系统采用的是UTF-16，那么单个Unicode字符在内存需要占用2个或者4个字节。

---

Q：能说说您对编码/解码的个人理解吗？

> 能。编码就是将字符集里面要表达的抽象的东西变成在内存/硬盘/网线里一个个实在的具体的字节序列。编码：code point/character -----> 字节序列 | 解码：字节序列 ------> code point/character。

> 其实解码看起来好像很简单，但别忘记了，对于计算机来说，`code point`/`character`是定义在Unicode标准里面的抽象的东西。我个人觉得解码是只可意会不可言传的。

```python
>>> b'\xe6\x88\x91\xe7\x88\xb1\xe4\xbd\xa0'.decode("utf-8")
'我爱你'
```

`b'\xe6\x88\x91\xe7\x88\xb1\xe4\xbd\xa0'`只是一个字节序列，但经过解码之后，python解释器就知道这个字节序列要表达的是“我爱你”了。

---

Q：Windows系统采用的是Unicode编码吗？

> 这个问题问的就有问题。目前Windows 10采用的是UTF-16 LE编码方式，支持/符合Unicode标准。

---

Q：那些系统/变成语言支持Unicode标准？

> 几乎现在所有系统和编程语言都直接/间接支持Unicode了。可以这么说吧：因为Windows系统和java都采用UTF-16，所以Windows和java都是支持Unicode标准的。因为python3采用了UTF-8,所以python3也支持unicode标准。


### ref

1. [Unicode](https://docs.python.org/3/howto/unicode.html)
2. [glossary](https://www.unicode.org/glossary/)
3. [Unicode 12.1.0](http://www.unicode.org/versions/Unicode12.1.0/ch03.pdf#G2212)
4. [Mapping and encoding](https://en.wikipedia.org/wiki/Unicode#Mapping_and_encodings)
5. [unicode-utf-ascii-ansi-format-differences](https://stackoverflow.com/questions/700187/unicode-utf-ascii-ansi-format-differences)
6. [why-is-1-byte-equal-to-8-bits](https://stackoverflow.com/questions/42842662/why-is-1-byte-equal-to-8-bits/42842817)
7. [utf_bom](https://www.unicode.org/faq/utf_bom.html)
8. [faq](https://www.unicode.org/faq/basic_q.html)
9. [大神的Unicode 文章](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)
10. [unicode-vs-ascii-memory](https://stackoverflow.com/questions/42168303/unicode-vs-ascii-memory)

