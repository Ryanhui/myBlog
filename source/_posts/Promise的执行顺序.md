---
title: Promise的执行顺序
date: 2018-02-14 08:23:03
tags: 前端
---

下面这个问题，考虑一下输出结果是怎样的

    new Promise(resolve => {
    	resolve(1);
    	Promise.resolve().then(() => console.log(2));
    	console.log(4);
	}).then( t => console.log(t) );
	console.log(3);

正确的结果为

	4 3 2 1
	
MDN中有如下解释：

> new Promise( /* executor */ function(resolve, reject) { ... } );
> 
> The executor function is executed immediately by the Promise implementation, passing resolve and reject functions (the executor is called before the Promise constructor even returns the created object). 

即

> 在New一个Promiese的时候，其中的executor函数立即执行，传递resolve函数和reject函数（executor会在Promise构造函数返回构造的对象前调用）。

第一行：代码从第一行开始运行，resolve内代码立即执行。

第二行：resolve(1) 作为promise的resolve，在then函数中赋值1给t，没有输出。

第三行：另一个promise，异步处理，代码向第四行继续执行。没有输出。

第四行：输出4，这是第一个输出。

第五行：同步执行，输出 3

第二个promise输出 2

第一个promise的resolve执行完毕，then输出 1