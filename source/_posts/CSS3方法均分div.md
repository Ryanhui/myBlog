---
title: CSS3方法均分div
date: 2017-09-11 14:04:15
tags: 前端
---
### 这个方法只能兼容比较新的谷歌火狐Safari和IE11以上的浏览器。

父容器的样式

    	/*盒模型*/
		display: -webkit-box;
		display: -moz-box;
		display: box;

		/*横向or纵向*/
		-webkit-box-orient: horizontal;   /*属性值：		[horizontal]横向/[vertical]纵向*/
		-moz-box-orient: horizontal;
		box-orient: horizontal;
		
子容器样式
    	
    	/*占有比例*/
		-webkit-box-flex: 1;
		-moz-box-flex: 1;
		box-flex: 1;