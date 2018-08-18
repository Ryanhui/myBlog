---
title: CSS3媒体查询
date: 2017-09-25 12:19:03
tags: 前端
---
### CSS2的 @media 规则使得为不同媒体类型定义不同的样式规则成为可能。

比如你可以用一套样式规则，一个适配电脑屏幕，一个适配打印机，一个适配手机，等等。

### CSS3中的媒体查询延伸了CSS2中的媒体类型的想法： 不是查看设备类型，而是查看设备特性。

媒体查询可以用来检查很多东西，比如：

* 视窗的宽高
* 设备的宽高（屏幕）
* 方向（纵向模式还是横向模式）
* 分辨率

使用媒体查询是为桌面设备，苹果和安卓适配不同样式的流行做法。

### 语法：

一个媒体查询包含一个媒体类型和一个或多个判断真假的表达式。

    @media not|only mediatype and (expressions) {
   		 CSS-Code;
	}
	
如果指定的媒体类型和设备正在显示的相同而且所有表达式为真，那么查询结果就是为真。

你可以为不同的媒体设置不同的样式表：

    <link rel="stylesheet" media="mediatype and|not|only (expressions)" href="print.css">
    
 
### 媒体类型：

* all ：用于所有媒体设备
* pring： 用于打印机
* screen： 用于电脑屏幕，平板，智能手机等
* speech： 用于屏幕阅读器

### 例子

使用媒体查询的一种方法是在样式表中插入备用的CSS内容

下面这个例子如果视窗大于480px，背景颜色就会变成lightgreen

    @media screen and (min-width: 480px) {
    body {
         	 background-color: lightgreen;
   		 }
	}
	
下面这个例子，如果视窗大于等于480px，菜单就会做浮动

    @media screen and (min-width: 480px) {
        #leftsidebar {width: 200px; float: left;}
        #main {margin-left:216px;}
	}

Done.