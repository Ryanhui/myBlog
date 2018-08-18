---
title: javascript 迭代方法
date: 2017-09-20 10:10:03
tags:
---

#### ES5中定义了5种迭代方法：
every() filter() forEach() map() some()

每个方法都接受两个参数：要在每一项上运行的函数和（可选）运行该函数的作用域对象（影响this的值）。

传入这些方法中的函数接受三个参数：数组项的值、该项在数组中的位置、数组对象本身。

every():对数组中的每一项运行函数，如果每一项都反回true，则返回true。

filter()：对数组中的每一项运行函数，返回该函数会返回true的项组成的数组。

forEach(): 对数组中的每一项运行函数，无返回。

map(): 对数组中的每一项运行函数，返回每次函数调用结果组成的数组。

some():对数组中的每一项运行函数，如果该函数对人一项返回true，则返回true。

以上方法都不会修改数组中包含的值。

### for-of是ES6中的新语法，用来配合迭代器。

    for (var key of table) {
 		 console.log(key + ' = ' + table[key]);
	 }

### 迭代器

    table[Symbol.iterator] = function () {
  		var keys = Object.keys(this).sort();
  		var index = 0;

  		return {
    		next: function () {
      			return {
        			value: keys[index], done: index++ >= keys.length
      				};
    			}
  			}
		}
	
其实并不是很懂2333