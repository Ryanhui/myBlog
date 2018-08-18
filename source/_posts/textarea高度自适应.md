---
title: Textarea高度自适应
date: 2017-09-25 19:56:27
tags: 前端
---
纯JavaScript代码

script

    function auto_grow(element) {
   		 element.style.height = "5px";
    	 element.style.height = (element.scrollHeight)+"px";
	}
	
CSS
   
    textarea {
  	  	resize: none;
    	overflow: hidden;
    	min-height: 50px;
    	max-height: 100px;
	}

html

    <textarea onkeyup="auto_grow(this)"></textarea>