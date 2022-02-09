---
sidebar: auto
title: css学习笔记
date: 2020-01-03 11:57:21
permalink: /pages/c9875d/
categories: 
  - 学习笔记
tags: 
  - css
---

# CSS 笔记

## 1. 盒子模型宽度如何计算

    offsetWidth = (内容宽度 + 内边距 + 边框)， 无外边距

## 2. margin 纵向重叠问题

-   相邻元素的 margin-top 和 margin-bottom 会发生重叠
-   空白的 `<p></p>` 也会重叠

## 3. margin 负值的问题

-   margin-top 和 margin-left 负值，元素向上、向左移动
-   margin-right 负值，右侧元素左移，自身不受影响
-   margin-bottom 负值，下方元素上移，自身不受影响

## 4. BFC 理解和应用

-   Block format context， 块级格式化上下文
-   一块独立渲染区域，内部元素的渲染不会影响边界以外的元素
-   形成 BFC 的常见条件：
    -   float 不是 none
    -   position 是 absolute 或 fixed
    -   overflow 不是 visible
    -   display 是 flex inline-block inline-flex inline-grid 等
-   BFC 常见应用：
    -   清除浮动

## 5. float 布局的问题， 以及 clearfix

圣杯布局和双飞翼布局的目的：

-   三栏布局，中间一栏最先加载和渲染（内容最重要）
-   两侧内容固定，中间内容随着宽度自适应
-   一般用于 PC 网页

圣杯布局和双飞翼布局的技术总结：

-   使用 float 布局
-   两侧用 margin 负值，以便 和中间的横向重叠
-   防止对中间内容的覆盖，一个用 padding 一个用 margin

### 5.1 圣杯布局

-   优先渲染中间元素，所以中间的 dom 放在第一个
-   容器内元素都设置 float: left; 并且清除浮动。
-   核心是设置容器左右两边的 padding 值等于两侧栏的宽度（中间一栏设置 100%宽度，以此实现自适应）
    然后利用 float 布局的特性，结合 margin 负值将两个侧栏位置分别挪回中间一栏的左右两边。
    左： margin-left: -100%; position: relative; right: (width);
    右： margin-right: -(width);

#### 5.2 双飞翼布局

-   基本原理同圣杯布局类似，不过比圣杯布局简单
-   不是在外容器设置 padding。而是给中间栏加一个子容器，在子容器上设置左右两边的 margin
-   由于中间栏的宽度是 100%，外容器没有内边距，所以右栏不能设置 margin-right 负值（这样会导致右栏挤到容器外），需要设置 margin-left 负值
    左： margin-left: -100%;
    右： margin-left: -(width)

### 5.3 手写 clearfix

```css
.clearfix:after {
    content: '';
    display: block;
    clear: both;
}
```

## 6. flex 画色子（三点的色子）

flex 常用属性

1. flex-direction
2. justify-content
3. align-items
4. flex-wrap
5. align-self

## 7. 水平居中对齐

1. inline 下 text-align: center;
2. margin: auto;
3. absolute 下 left: 50%; margin-left: 负值
4. flex 下： align-items: center;

## 8. 垂直居中对齐

1. inline 下， line-height 设置成跟 height 一样
2. top: 50%; relative + margin-top: 负值
3. margin-top: 50%; transform: translateY(-50%);
4. absolute 的话， top bottom left right 都设置为 0， + margin: auto;**_兼容性最好，而且不需要知道子元素的宽度高度_**

## 9. `line-height` 如何继承

-   具体数值，如 16px，20rem，继承该值
-   写比例，如 2/1.5， 则继承比例
-   写百分比，如 200%，计算出值之后继承值

## 10. 响应式

### rem 是什么

    em: 相对于父元素的长度单位
    rem: 相对于根元素的长度单位

### 响应式布局的常见方案

-   media query， 给 html 设置 font-size 和使用 rem 实现响应式
-   vh/vw 视口的百分之一

> window.screen,height // 屏幕高度
> window.innerHeight // 视口高度
> document.body.clientHeight // body 高度
