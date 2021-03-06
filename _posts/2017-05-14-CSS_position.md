---
layout: post
title: css笔记
date: 2017-05-14 11:29:08 +0800
tags: [css]
---


### 定位模型

- Flow（流动模型）（默认）
- Float（浮动模型）
- Layer（层模型）


### 定位方式
1. static定位：没有特别的设定，遵循基本的定位规定，不能通过z-index进行层次分级。
2. relative定位：对象不可层叠、不脱离文档流，参考自身静态位置通过top,bottom,left,right 定位，并且可以通过z-index进行层次分级。
3. absolute定位： 脱离文档流，通过 top,bottom,left,right 定位。选取其最近一个最有定位设置的父级对象进行绝对定位，如果对象的父级没有设置定位属性，absolute元素将以body坐标原点进行定位，可以通过z-index进行层次分级
4. fixed定位： 这里所固定的参照对像是可视窗口而并非是body或是父级元素。可通过z-index进行层次分级。


- 如果找到一个设置了position:relative/absolute/fixed的元素，
就以该元素为基准定位，如果没找到，就以浏览器边界定位,
- absolute元素是根据最近的定位上下文确定位置，
而fixed永远根据浏览器确定位置
- 不会受空间限制，会超过容器尺寸
- absolute元素脱离了文档结构。和relative不同，其他三个元素的位置重新排列了。
- 只要元素会脱离文档结构，它就会产生破坏性，导致父元素坍塌。
- （此时你应该能立刻想起来，float元素也会脱离文档结构）
- absolute元素具有“包裹性”。之前`<p>`的宽度是撑满整个屏幕的，
- 而此时`<p>`的宽度刚好是内容的宽度。
`如果该元素是块级元素，元素的宽度由原来的 width: 100%（占据一行），
变为了 auto`
- absolute元素具有“跟随性”。虽然absolute元素脱离了文档结构，
- 但是它的位置并没有发生变化，还是老老实实的呆在它原本的位置，
- 因为我们此时没有设置top、left的值。
- absolute元素会悬浮在页面上方，会遮挡住下方的页面内容
- 设置absolute会使得inline元素被“块”化，这在上一节将display时已经说过；
`该元素将变为块级元素，相当于给该元素设置了 display: block`
- 设置absolute会使得元素已有的float失效。不过float和absolute同时使用的情况不多；
- 上文提到了absolute会使元素悬浮在页面之上，如果有多个悬浮元素，层级如何确定？
- 答案是“后来者居上”


----

### 块级元素：

1. 每个块级元素都从新的一行开始，并且其后的元素也另起一行。（真霸道，一个块级元素独占一行）
2. 元素的高度、宽度、行高以及顶和底边距都可设置。
3. 元素宽度在不设置的情况下，是它本身父容器的100%（和父元素的宽度一致），除非设定一个宽度




```   

	<address>联系方式信息。                
	<article>文章内容。 
	<aside> 伴随内容。 
	<audio>音频播放。 
	<blockquote>块引用。 	
	<canvas> 绘制图形。 
	<dd>定义列表中定义条目描述。 
	<div>文档分区。 
	<dl> 定义列表。 
	<fieldset>表单元素分组。 
	<figcaption> 图文信息组标题 
	<figure> 图文信息组 
	<footer> 区段尾或页尾。 
	<form>表单。 
	<h1>, <h2>, <h3>, <h4>, <h5>, <h6> 标题级别 1-6. 
	<header>区段头或页头。 
	<hgroup> 标题组。 
	<hr>水平分割线。 
	<noscript> 不支持脚本或禁用脚本时显示的内容。 
	<ol>有序列表。 
	<output> 表单输出。 
	<p>行。 
	<pre>预格式化文本。 
	<section> 一个页面区段。 
	<table> 表格。 
	<tfoot>表脚注。 
	<video> 视频。       
	<ul>无序列表。    
```           






---

###  行级元素：

1. 和其他元素都在一行上；
2. 元素的高度、宽度及顶部和底部边距不可设置；
3. 元素的宽度就是它包含的文字或图片的宽度，不可改变。

```
	b, big, i, small, tt 
	abbr, acronym, cite, code, dfn, em, kbd, strong, samp, var 
	a, bdo, br, img, map, object, q, script, span, sub, sup 
	button, input, label, select, textarea
```

--- 

### 盒子模型

- 加上box-sizing: border-box;之后，设完width，再设padding，和border的宽度，就会挤压content了，不会撑开。

### 弹性盒子 flex

- flex-direction 设置fb子元素的方向
- flex-wrap 设置折行模式
- flex-flow 合并上两个属性
- justify-content 设置子元素在主轴的对齐方式
- align-content 设置子元素在交叉轴的对齐方式































































