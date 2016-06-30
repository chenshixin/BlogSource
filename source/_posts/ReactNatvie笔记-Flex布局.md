title: ReactNative笔记-Flex布局
date: 2016-01-28 10:31:59
tags: [ReactNative, Flex]
description: "ReactNative内的样式采用FlexBox，本文记录学习过程中遇到的Flex的基本概念，属性及一些简单的布局实现方式。"
---
# 简介
ReactNative基于FlexBox实现页面布局，FlexBox布局是CSS3中一种的新的布局模块，其主要思想是Flex元素能够自动放大缩小来填充剩余可用空间。与传统的布局不同，Flex布局中方向并不是固定的，横着竖着或者反向横竖都可用轻松实现。学习Flex布局需要了解其基本的布局概念，熟悉容器属性及元素属性，在此基础上尝试实现常见布局形式。
** `特别注意：
1以下的属性名为FlexBox为CSS3中的属性名，在React-Native中使用时，需要转化为驼峰式的命名方式。如flex-direction需要写为flexDirection的形式。
2align-Content在ReactNatvie中不存在` **
本文内容和图片均来自参考资料中的文章，仅做整理。

# 基本概念
![basic](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_basic.png)
**Flex容器（Flex Container）**：采用Flex布局的容器
**Flex元素（Flex Item)**：采用Flex布局的子元素
**主轴 (Main Axis)**：Flex元素排列的方向。其方向由`flex-direction`决定
**交叉轴（Cross Axis）**：与主轴垂直方向
**主轴起点（Main Start）| 主轴终点（Main End)**：主轴方向由主轴起点开始，往主轴终点结束
**交叉轴起点（Cross Start）| 交叉轴终点（Cross End）**：交叉轴方向由交叉轴起点开始，往交叉轴终点结束
# Flex属性
Flex属性分为容器属性和元素属性两类。分别作用于Flex容器和Flex元素上。
## 容器属性
容器属性决定容器内部元素的排列方式和方向，目前容器属性共6种，分别是：

### flex-direction
flex-direction属性是项目排列的方向，即主轴方向。
flex-direction可选值为 `row（默认值） | row-reverse | column | column-reverse`
![flex-direction](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_flex-direction.svg)
- row:水平方向从左到右
- row-reverse:与row相反，水平方向从右到左
- column:竖直方向从上往下
- column-reverse:竖直方向从下往上

### flex-wrap
flex-wrap属性定义了当一条轴线排列不下元素时，换行的方式。默认情况下元素排列在一行（nowrap），通过定义flex-wrap可以进行换行
flex-wrap可选值为 `nowrap（默认值） | wrap | wrap-reverse`
![flex-wrap](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_flex-wrap.svg)
- nowrap:不换行
- wrap:换行，第一行在上方
- wrap-reverse:换行，第一行在下方

### flex-flow
flex-flow是一种简写形式，其形式为flex-direction || flex-wrap，第一个值是flex-direction，第二个值是flex-wrap,默认值为row nowrap

### justify-content
justify-content是元素沿主轴方向的对齐方式
justify-content可选值为`flex-start(默认值) | flex-end | center | space-between | space-around`
![justify-content](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_justify-content.svg)
- flex-start:主轴起点方向对齐
- flex-end:主轴终点方向对齐
- center:居中对齐
- space-between:元素平均分布在主轴上，元素 **之间** 的间隔相等
- space-around:元素平均分布在主轴上，元素 **两侧** 的间隔相等，因此元素之间的距离是元素与边框距离的两倍(注意与space-between的区别)

### align-items
align-items是元素在交叉轴上的对齐方式（相当于在交叉轴是上的justify-content）
align-items可选值为 `flex-start | flex-end | center | baseline | stretch(默认值) `
![align-items](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_align-items.svg)
- flex-start:交叉轴起点方向对齐
- flex-end:交叉轴终点方向对齐
- center:居中对齐
- baseline:按照基准线对齐([基准线的计算](https://www.w3.org/TR/css-flexbox/#flex-baselines))
- stretch:元素占满交叉轴（但是仍然受到min-width和max-width的限制）

### align-content（ReactNative中不存在此属性）
align-content定义了在交叉轴上有多行元素的情况，类似于justify-content中有多行元素时候的对齐方式
align-content可选值为 `flex-start | flex-end | center | space-between | space-around | stretch(默认值)`
![align-content](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_align-content.svg)
- flex-start:交叉轴起点方向对齐
- flex-end:交叉轴终点方向对齐
- center:居中对齐
- space-between:元素 **之间** 间隔相当
- space-around:元素 **两侧** 间隔相当
- stretch:占满交叉轴


## 元素属性
目前flex元素属性共有6中，分别是：

### order
order定义了元素在flex容器中出现的顺序，order数值越小，元素出现的越靠前。默认值为0
order是一个 **可为负** 的整数
![order](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_order.svg)

### flex-grow
flex-grow属性定义了当容器有剩余空间时，内部元素的放大比例，默认为0，即如果存在剩余空间，也不放大
如果所有元素flex-grow都设置为同一个数字，则他们会平分剩余的空间。
flex-grow是一个 **非负** 整数
![order](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_flex-grow.svg)

### flex-shrink
与flex-grow相对的，flex-shrink定义了当容器空间不足时，元素的缩小比例，默认值为0，即空间不足时也不缩小
flex-shrink是一个 **非负** 整数

### flex-basis
flex-basis属性定义了在分配多余空间之前，元素占据的主轴空间（main size），默认值为auto，即元素的本来大小。
flex-basic可以是一个数值（20%，5em等）或者关键词（auto等）

### flex
flex是 flex-grow, flex-shrink, flex-basic的简写（推荐使用这种方式），其中flex-shrink和felx-basic是可以省略的，默认值为0 1 auto

### align-self
align-self可以覆盖默认的对齐方式或者通过align-items指定的对齐方式，默认值为auto，即继承父元素的align-items属性
可选值为auto加上align-items的5个可选值
![order](http://7xored.com1.z0.glb.clouddn.com/blog_react_flex_align-items.svg)

# 参考资料
- [A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
- [A Visual Guide to CSS3 Flexbox Properties](https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties)
