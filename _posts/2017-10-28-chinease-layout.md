---
layout: post
title:  简单做好中文排版 | 转
category: literature
tags: modern_times
---
![](https://cdn.kelu.org/blog/tags/literature.jpg)

# 简单做好中文排版

#### 十项让长文章更容易阅读的原则

董福兴

* * *

> 本文最初以英文发表于 [Medium](https://medium.com/@bobtung/best-practice-in-chinese-layout-f933aff1728f)，主要是希望给外文圈的朋友在进行 Web 与 App 等内容服务中文地区化时，能够提供较好的文字排版呈现。在吴逸文、许翰文等设计圈的朋友催促下，以中文重新书写，删除部分“对外”的用语，并且加上一些额外的资讯，希望对中文圈也能有所助益。

* * *

东亚对于全球化的网路服务来说，进入时会遇到几道墙壁隔离，第一道就是语言的障蔽（然后你会遇到中国伟大的防火墙）。中文、日文、韩文有着不同的排版规则，目前在 W3C 的参考资料中，[日文排版需求](http://www.w3.org/TR/jlreq/)是最为完整的文件，但是大概长到很难读完；韩国的[谚文文字排版需求](http://www.w3.org/TR/klreq/)则是份量刚刚好。至于中文，我目前还在撰写草稿。

在我写完前，先整理出十项简单的原则，作为做好中文排版的参考。

## 一、注意标点的不同

无论你有没有安装额外的字型，各作业系统中有汉字的字体数量不在少数，有些是简体、有些是繁体、有些是日文。_感谢_ Unicode 早期的中日韩越统一表意文字 [1](http://binb.tw/ebookmag/00/00.html#fn:1 "see footnote") 规划，这些字体大致上有着相当的共通性。

但问题在于，日文、繁体中文与简体中文的字形（Glyph）[2](http://binb.tw/ebookmag/00/00.html#fn:2 "see footnote") 不同，加上每种字型只会造出针对该种语言常用的汉字 [3](http://binb.tw/ebookmag/00/00.html#fn:3 "see footnote")，常会发生缺字而后退（Fallback）到其他字型的状况，会使得文内的字型不一致而打乱阅读。所以，从一开始就选对字型相当重要。

那么，要如何判断简体与繁体的字型呢？

很简单，输入一个全形的逗号与句号，套用你所要的字型，若这两个符号位于正中央，就是繁体中文；若日文一样落到左下角，就是简体中文。若要进一步区分简体中文与日文字型，可以输入日文没有的汉字，例如“启（U+555F）”，若无法正常显示，就是日文字型。

最后，如果没有特别的原因 [4](http://binb.tw/ebookmag/00/00.html#fn:4 "see footnote")，中文原则上都使用全形标点。

![](https://cdn.kelu.org/blog/2017/10/001.png)

繁体与简体标点的位置不同

> 补充说明：有朋友来信询问这一段的叙述。标点符号位置只是用来判断简体字型与繁体字型的简单依据。并非繁体字一定要使用置中标点才是对的。[5](http://binb.tw/ebookmag/00/00.html#fn:5 "see footnote")

## 二、使用正确的系统字型

更进一步，你可以在 App 的地区化或者 CSS 中指定对的系统字型。我常看到许多产品只做好日文的地区化，例如说仅使用 OS X 与 iOS 中的 Hiragino Mincho 来做内文字，这会产生许多问题：

*   标点不符合繁体中文规则。
*   前面所提缺字会让内文遇到一些字时显得坑坑疤疤，就像剪贴的黑函一样。
*   台湾、日本、香港、中国使用的字形不同，而最好都能用该区域的标准字。

![](https://cdn.kelu.org/blog/2017/10/002.png)

“角”字简繁之间有字形差

下面是 OS X / iOS / Windows 与 Android 中的中文系统字型，让你能够确实选对。有一些只预装在新版系统 [6](http://binb.tw/ebookmag/00/00.html#fn:6 "see footnote") 上，而在 iOS App 上，可能要做些[额外的工夫](https://developer.apple.com/Library/ios/samplecode/DownloadFont/Listings/DownloadFont_ViewController_m.html#//apple_ref/doc/uid/DTS40013404-DownloadFont_ViewController_m-DontLinkElementID_6)才能从 Apple 下载这些字型使用。

![](https://cdn.kelu.org/blog/2017/10/003.png)

各作业系统中文系统字型列表

对于 Android 来说，_Droid Sans Fallback_ 这套系统字无论对中日韩文来说都不大及格，如果希望达到较好的排版效果，建议使用开源的“[思源黑体](https://www.google.com/get/noto/#/)”但下载任何有汉字的字形都要花上不少时间，除非预先 Subset 来轻减。你也可以使用一些动态 Subset 的 Webfont 服务，或者干脆忘了 Android 系统（哎！）。

> 补充说明：在 HTML 5 中可以 lang 设定网页语言，中文的代码是 zh，过去常用的是 zh-hant 与 zh-hans 来区分简繁体。但我更建议使用 zh-TW、zh-CN、zh-HK 来加上地区。虽然现在没有显著的差异，但香港的粤语造字未来可能从繁体中文中分离，加上地区描述未来可能会用得上，且向下相容。

## 三、适当的行距

不只是行距，字级也是个问题。但我没办法告诉你字级要多大才正确，毕竟现在荧幕尺寸与 DPI 差距颇大，但绝对不能以排英文的方式来排中文。活版时代，内文常用的字级有两种，一种是_五号字_，就是 10.5pt（3.7mm），另一种是_新五号_，就是 9pt（3.18mm）。[7](http://binb.tw/ebookmag/00/00.html#fn:7 "see footnote") 内文字尽量不要小于 9pt。这里请以适当的大小自行计算，毕竟荧幕与书籍是不同的。

但是行距有着_正确的数字_，一般而言中文行距介于 1.5 到 2em 之间，通常只要指定：

```
p {line-height: 1.7em;}

```

就能得到适当的行距。

## 四、对齐是万灵丹

![](https://cdn.kelu.org/blog/2017/10/004.jpg)

传统中文活版

这张图片是古老的中文活字印刷版，从这里可以显现出重要的中文排版原则：所有的元素都是正方体。

但是从二十世纪开始使用标点后，到了现代桌上出版时代，许多排版工具软体都直接套用来自日本的“禁则处理”—即避头尾点；加上与西方文字混排的状况越来越多，以至于无法做到纵横对齐的基础。但是至少段落的头尾还是需要对齐。这就是为什么对齐对电子书与长文章来说十分重要的原因。

你可以使用以下 CSS：

```
p { text-align: justify; 
    text-justify: ideographic;}

```

这能让中文排版瞬间变得美观许多。

## 五、没有斜体

中文的书写、印刷历史中，“斜体”从来都不存在。拉丁文字中所称的“italic”主要是指“手写体”[8](http://binb.tw/ebookmag/00/00.html#fn:8 "see footnote")，但在中文传统中，手写体就是书法字，更贴近“Cursive”的定义，无论楷书、行书、草书都该属于这类别。

![](https://cdn.kelu.org/blog/2017/10/005.gif)

于右任草书

但到了数位时代，硬套用拉丁文字的 italic 到中文上才开始出现了斜体字，这斜体字称为 oblique，也就是强制转斜。这并不是个好作法。但在 HTML 中，有许多标签预设就会强制把字转斜，若发生这种状况，就会需要利用 CSS 来更正：

```
em { font-style: normal; }

```

若你要使用 <em> 强调单字时，可以加粗、改成黑体、加底线或强调点，但就是不应该用斜体。

## 六、段落区隔

段落区隔对于中文而言相当重要，有着两种方法：

### 1\. 如书的呈现

中文的印刷书一般段落之间除非有其用意，不然不会加入空行或者间隔来区分段落，而是使用两个全形空白（杂志等窄栏时使用一个）来缩排做出段落区隔。在分页的情形下，可以在换页时也能轻易地看出段落区隔，CSS 的写法是：

```
p { margin: 0; 
    text-indent: 2em;}

```

日本电子书业界则是常用全形空白（U+3000, ideographic space）来取代 text-indent。这可以避免在不同环境下都能有相同的表现，并且对齐得更适当。遇到一些阅读工具，像是 Safari 浏览器的阅读器时，也不会因为 CSS 被取代而让缩排消失，这种方法可以保持。[9](http://binb.tw/ebookmag/00/00.html#fn:9 "see footnote")

### 2\. 如网页的呈现

但人们在网页上阅读速度较快，如书般的版面会让字排得过于密集产生压力，通常使用 margin-after（或 margin-bottom）来区分：

```
p { margin-after: 0.5em;}

```

虽然段落间要以多少空白区分没有定论，但建议介于 0.5 到 1em 间，不要加入太多空白为佳。[10](http://binb.tw/ebookmag/00/00.html#fn:10 "see footnote")

## 七、楷体更像书

一般中文内文常用 serif 字体，像是明 / 宋体。虽然 sans-serif 字体更_现代_，但在印刷书的世界里，却鲜少看到。[11](http://binb.tw/ebookmag/00/00.html#fn:11 "see footnote")

![](https://cdn.kelu.org/blog/2017/10/006.png)

传统印刷书使用楷体的状况
出处：大块文化《叶嘉莹作品集》

而一般中文印刷书中的：摘要、摘句、引言、对话、独白、诗词等，都会使用楷体来表示。所以若要让文章读起来更像印刷书，使用楷体会是不错的办法。

当然啦，使用黑体也是能令人接受的。

## 八、避头点 v.s. Break-all

对齐（Justification）是让文章符合中文排版原则的数位解决之道，但这方法不是时时完美。有一个简单能够重现的问题：

![](https://cdn.kelu.org/blog/2017/10/007.png)

对齐拉宽字距

![](https://cdn.kelu.org/blog/2017/10/008.png)

Break-all 强制对齐

1\. 在杂志排版的窄栏，或者手机荧幕上；

2\. 在中文字中有着一个或多个长的拉丁字；

3\. 使用对齐。

就会看到如图中的样貌，字距被强制扩展，甚至超过一个字。这不仅出现在浏览器上，桌面排版工具亦然。

有个简单的处理方法，只要加上：

```
p { word-break: break-all; }

```

就能改善许多。但这种做法会让西方文字被强制切断，不甚完美。同时也会无视避头点规则，让逗号、句号出现在行头，繁体中文可以接受，但不能用于简体。

为什么？因为简体中文的标点如日文一般位于左下角，当他们出现于行头时会显得极为奇怪。但位于中央的繁体中文标点却还可以接受。

## 九、注意字距

对于中文文章的内文而言，你不需要调整字距。有些香港的网站会为文章加上字距，但绝对不是好的做法。

![](https://cdn.kelu.org/blog/2017/10/009.png)

增加字距会让读者无法确认行文方向

为什么？别忘了中文是双向文字，你可以由上往下读，也可以由左向右读。行距是提供读者行文方向的重要依据，若你加上字距，就得加大行距，最后让文章变得不能阅读。当然不会加字距加得那么夸张，但为了提供易读性，请让字距保持为 0。请记得：

> 不要调整内文的字距，但标题可以变动。[12](http://binb.tw/ebookmag/00/00.html#fn:12 "see footnote")

## 十、繁转简没问题，简转繁不 ok

![](https://cdn.kelu.org/blog/2017/10/010.png)

简繁常用字的对应表

这是简体与繁体中文常用字的对应表。虽然简体与繁体许多字并不使用相同的码位，但大多数的转换工具都能透过对应表来简单匹配。

不过问题来了，这表格中最大的问题就是那 267 个_一简多繁_的字。在转换时若不使用字典作为辅助工具，就会出现很大的问题，像是：

*   繁→简：皇后、後世→皇后、后世（◯）
*   简→繁：皇后、后世→皇后、后世（×）
*   繁→简：吕布→吕布（◯）
*   简→繁：呂布→呂佈（×）

所以，简转繁，不校不行。[13](http://binb.tw/ebookmag/00/00.html#fn:13 "see footnote")

以上就是简单的排版作法，花不上太多时间就能让文章排得易于阅读，不妨一试。

> 补充说明：本文中未提及中文与拉丁文字混排时的间距问题。目前技术上还未能有最佳处理方法，待日后再提及。[14](http://binb.tw/ebookmag/00/00.html#fn:14 "see footnote")

✜

* * *

## 注释

1.  当 ISO 10646 在 1990 年初期规划时，因为采用 16 位元编码，仅有 65,536 个码位（Codepoint），自然无法容纳全部的汉字，于是第一版将中、日、韩文中类似的汉字纳于同样的码位，不会因为地区的字型差而分离，之后扩充时才逐渐分离。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:1 "return to article")
2.  因为字形与字型口语上容易搞混，我个人倡议将描述字长得怎么样、笔画怎么写的“Glyph”称为“字貌”。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:2 "return to article")
3.  例如日文仅会按照印刷需求制作 [Adobe Japan 1–6](http://www.adobe.com/content/dam/Adobe/en/devnet/font/pdfs/5078.Adobe-Japan1-6.pdf)的汉字，繁体中文一般为 BIG 5 码基准，部分会扩张到 Unicode 3.0 标准，含 CJK Ext-A 在内约三万字。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:3 "return to article")
4.  例如有些常出现英文段落的论文或者技术文章，为求标点一致，会全部使用半形的标点。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:4 "return to article")
5.  中文印刷书早期的标点多至于内文行侧，如过去句读般。但新青年杂志在 1919 年倡议使用新式标点时，就已经将标点置中了。也有作家认为标点应该留下中文句读的传统，如日文一般直排置于旁侧，那人就是李敖。不过这些论点主要还是直排书的标点应用。至于横排，书写文字时，大多数的人会将标点写在下方，但印刷书还是置于中央。内容要使用哪一种标点，我觉得应该让设计者自行决定。CSS 里可以使用 @font-face 的 unicode-range 来指定另一种字型的标点。但我认为，在一篇文章中，标点位置需要一致，一段左下、一段置中会显得相当奇怪。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:5 "return to article")
6.  例如微软正黑体在 Windows Vista 后才有，宋体繁在 OS X Maverick 后才有得用。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:5 "return to article")
7.  早期书籍常用五号字，但后来受到报纸影响，逐渐改用新五号字。现在市面上的印刷书少见五号字，不过 9pt 的新五号字实在太小不易阅读，在数位环境下，我建议放大些，别让人们读得太痛苦。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:7 "return to article")
8.  在日本曾有这样的讨论，若要把 italic 的定义套用到汉字上，该怎么处理？他们认为明朝体（即中文的宋 / 明体）的 italic 应该是宋朝体（即中文的仿宋体），这可以透过 @font-face 指定办到。但多数的系统没有仿宋体，需要另外嵌入。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:8 "return to article")
9.  但请注意，不要用了两个全形空白又用 text-indent，这会让段首退后四字，显然太多了！ [↩](http://binb.tw/ebookmag/00/00.html#fnref:9 "return to article")
10.  写作时也需注意，有时作者会自己加入空行作为段落区隔，但在发表时空行会转换成 <br> 换行标签，造成段落中额外的空白，这是多余的。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:10 "return to article")
11.  我相信这段陈述会引起相当的辩论。但不可否认地在印刷书的世界，内文字就应该是明 / 宋体。中文在数位的世界里并不顺遂，过去二、三十多年来遇到荧幕品质、描绘技术、字型技术等问题，加上汉字数量多，有得用就不错了。所以人们一直认为新细明体丑、标楷体死板。加上行动装置多半只有黑体可用，造成黑体在数位的世界里用得过于泛滥。我觉得在现在环境成熟下，应该回到传统，重新利用明 / 宋体做些排版的实践 / 实验。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:11 "return to article")
12.  英文排版学中有个术语叫做“Kerning”，日文有相对的词汇叫做“文字诘め”，但中文没有适当的对应术语。中文手写上当然不会字字对齐，但印刷上始终如此。我个人认为迄今中文排版不应该做任何字距的调整，但平面设计、标语设计与标题等可自由发挥。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:12 "return to article")
13.  这一段主要是讲给老外听的。但有朋友提供了[开放中文转换](https://github.com/BYVoid/OpenCC)的开源工具，不妨一试。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:13 "return to article")
14.  目前文字间距在技术上还没有最佳的解法，像是 Unicode 里定义了[好几种宽度的空白](http://en.wikipedia.org/wiki/Whitespace_character)，但不是每套字都实作了，所以并非好的解法。而使用 Javascript 等自动加入，或者在撰写文章时手动加入空白也并非好的方法。现阶段 CSS Text Level 4 的 Property [text-spacing](http://dev.w3.org/csswg/css-text-4/#text-spacing) 里有一项 ideographic-alpha 就是用来加入 1/4em 空白的值，但 1/4em 空白是日文自活字印刷以来的标准，中文需要怎么样的处理，还需要另外想想。但总之，到实作为止还需要一段时间。至少，我不建议手动加入空白。 [↩](http://binb.tw/ebookmag/00/00.html#fnref:14 "return to article")

## 作者简介

董福兴是 WANDERER 数位出版创办人，W3C 邀请专家。目前正在撰写 W3C i18n 工作小组〈中文排版需求〉文件。也是 EPUB 3.0 格式之专家，提供电子书制作工具、编辑流程顾问与制作技术教学等服务。

* * *

# 参考资料

* [简单做好中文排版](http://tookdes.org/article/简单做好中文排版.html)