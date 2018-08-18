---
title: JavaScript 模块详解：CommonJS vs AMD vs ES2015
date: 2017-09-09 16:43:44
tags:
---
>随着js的发展，命名空间和依赖关系越来越难处理。许多处理办法应运而生，本文就探索了目前不同的应对模块的方法。

## 为什么需要模块化？

如果你熟悉其他语言，你会注意到有封装和依赖的内容。软件的不同部分单独开发，需要时便组装到一起，这样避免冲突就很重要。

封装不仅可以防止冲突，同时也能简化开发。

在处理依赖模块时，传统的浏览器端开发中，解决依赖关系是开发人员的工作。需要保证模块的加载顺序正确，就比如这个例子：
    	
    <!DOCTYPE html>
	<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Backbone.js Todos</title>
        <link rel="stylesheet" href="todos.css"/>
    </head>

    <body>
        <script src="../../test/vendor/json2.js"></script>
        <script src="../../test/vendor/jquery.js"></script>
        <script src="../../test/vendor/underscore.js"></script>
        <script src="../../backbone.js"></script>
        <script src="../backbone.localStorage.js"></script>
        <script src="todos.js"></script>
    </body>

    <!-- (...) -->
	</html>
	
随着js越搞越复杂，这东西就越来越乱。重构时就有问题了：我这个新的依赖该放哪里才对？

所以就有了下面这堆方法。

## 一个有特色的方法

叫 The Revealing Module Pattern，我不知道怎么翻译，谷歌翻译过来是显示模块模式。

就是在很久以前，人们没有办法，想出了这个东西。一个函数。

	var myRevealingModule = (function () {
    var privateVar = "Ben Cherry",
        publicVar = "Hey there!";

    function privateFunction() {
        console.log( "Name:" + privateVar );
    }

    function publicSetName( strName ) {
        privateVar = strName;
    }

    function publicGetName() {
        privateFunction();
    }

    // Reveal public pointers to
    // private functions and properties
    return {
        setName: publicSetName,
        greeting: publicVar,
        getName: publicGetName
    };
	})();

	myRevealingModule.setName( "Paul Kinlan" );


在ES6之前，JS中作用域之在函数级别。换句话说，函数内绑定的内容不会被函数外访问到。这样，我们不就有私有的空间了嘛。

上面那个例子里，我们把模块都放在函数里，然后把需要暴露的return出去，这样大功告成。并且没有必要用var和立即执行函数，命名函数也是可以的。

对于封装的问题，这个解决方法还是不错的，但是依赖加载似乎就没啥用。

*优点：简单，多个模块可以在一个文件中。*

*缺点：没有办法科学导入模块。依赖需要手动加载。不可能异步。环状依赖会有大麻烦。很难应用静态模块分析。*

## CommonJS

CommonJS项目的诞生是为了规范化服务器端JavaScript开发，其中就包括模块化。Node.js的开发者一开始打算遵循CommonJS的规范，但是后来决定反对。

涉及到模块时，Node.js的实现会非常受影响：
    	
    	// In circle.js
    	const PI = Math.PI;

    	exports.area = (r) => PI * r * r;

    	exports.circumference = (r) => 2 * PI * r;

    	// In some file
    	const circle = require('./circle.js');
    	console.log( `The area of a circle of radius 4 is ${circle.area(4)}`);

在库上面一些抽象的模块系统弥补了Node.js模块系统和CommonJS之间的隔阂。比如在Node和CommonJS的模块里面都实质上有两个元素：require和exports。require可以从其他模块导入到当前环境。传递给require的参数是模块的id。在Nodejs中，是存在node_modules目录中模块的名字。exports是一个特别的对象：任何在里面的内容会作为公有元素导出。字段名字被保留。一个Node和CommonJS之间奇特的不同就出现在module.exports对象的构成。在Node中，moudle.exports是一个真正特殊的对象并被导出，exports只是一个变量默认绑定到module.exports。CommonJS却不一样，它没有module.exports对象。实际上就是在Node中不可能不通过module.exports导出一个完全预先构建好的对象。

    	// This won't work, replacing exports entirely breaks the binding to
    	// modules.exports.
    	exports =  (width) => {
      		return {
        		area: () => width * width
      		};
    	}

    	// This works as expected.
    	module.exports = (width) => {
      		return {
        		area: () => width * width
      		};
    	}

CommonJS设计应用在服务器端，自然API都是同步的，所以模块按照在源文件里的顺序实时加载。

*优点：简单，开发者可以不用看文档就掌握概念。依赖管理是集成好的，模块按照顺序加载其他模块。require可以在任何地方被调用，模块可以按程序调用。循环依赖可以被支持。*

*缺点：同步的API不适合浏览器端。一个模块一个文件。浏览器需要一个加载的库。没有构造函数。很难静态代码分析。*


### 实现

Node.js

在浏览器端有两个主流选择：webpack和browserify。

Browserify 就是为了解析类似于Node模块定义方式定义的模块，把你的代码和模块打包成一个文件，一个文件包含所以的依赖。

Webpack则是为了在发布前将复杂的代码依赖等处理的工具，包括打包CommonJS的模块。

## 异步模块定义（AMD）

AMD是由一群开发者因为不满CommonJS的开发方向而自己开发出来的。事实上，AMD在其开发早期从CommonJS分离。AMD和CommonJS主要的不同是在于它支持异步加载。

    	//Calling define with a dependency array and a factory function
    	define(['dep1', 'dep2'], function (dep1, dep2) {

        	//Define the module value by returning a value.
        	return function () {};
    	});

    	// Or:
    	define(function (require) {
        	var dep1 = require('dep1'),
            	dep2 = require('dep2');

        	return function () {};
    	});


异步加载让模块加载完成后立即调用一个函数成为可能。模块定义和导入一个模块可以由一个函数完成：一个模块定义的时候它的依赖也可以明确知道。AMD加载器因此在项目运行时有一个完整的依赖的图表。库不要互相依赖所以可以同时加载。这对浏览器来说很重要，启动时就有很好的体验。

*优点：异步加载。支持循环依赖。兼容require和exports。集成依赖管理。模块可以分割。构造函数被支持。支持插件（传统加载步骤）。*

*缺点：语法稍复杂。需要加载的库。很难静态代码分析。*

### 应用

最流行的事require.js和Dojo

使用require.js的方式很直接：在HTLML中引入库，使用data-main属性告诉require.js哪个模块最先被加载。Dojo步骤相同。

## ES2015 模块

幸运的是，ECMA团队决定解决模块问题。在ES6中就能看到成果。语法简单并且支持同步和异步。

    	//------ lib.js ------
    	export const sqrt = Math.sqrt;
    	export function square(x) {
        	return x * x;
    	}
    	export function diag(x, y) {
        	return sqrt(square(x) + square(y));
    	}

    	//------ main.js ------
    	import { square, diag } from 'lib';
    	console.log(square(11)); // 121
    	console.log(diag(4, 3)); // 5

import命令可以把模块加载到命名空间。这个命令和require以及define对比发现它不是动态的（即不能在所有地方调用）。export则可以明确的让元素变为公有。

import和export静态的特性让静态代码分析变为可能，不通过运行代码就可以建立一个完整的依赖树。ES2015还不支持动态模块加载，但是在草稿上是有的：
    	
    	System.import('some_module')
      .then(some_module => {
          // Use some_module
      })
      .catch(error => {
          // ...
      });

事实上，ES2015只明确了静态模块加载的语法。实践中，解析这些命令后，ES2015命令实现不做任何事。模块加载器还是需要的，比如System.js。浏览器模块加载在草案中可用。

这个方案，凭借融入语言的方式，成为运行时最佳模块加载策略。就是说，异步加载有优势是，它可以用在任何时间。

*优点:同步异步都被支持。语法简单。支持静态分析工具。集成在语言中，最终会被全面支持，不需要库。循环依赖被支持。*

*缺点：还没有被各种环境都支持。*

### 应用

不幸的事，还没有稳定版的主流运行环境支持ES6。所以Firefox，Chrome，Nodejs都不行。但是有很多转换工具已经支持，现在，ES6内置的Babel可以解决模块问题。

## 一体化解决方案 System.js

你可能会发现你尝试着使用模块系统从遗留的代码中脱身。或者你想确定到底发生了什么，你选择的方案依旧有效。System.js，一个通用的模块加载，适用于Commonjs，AMD，ES6模块。它能和Babel或Traceur类似的转换工具一起工作，支持Node和IE8+环境。使用只需要加载System.js然后指向根地址：

    <script src="system.js"></script>
    <script>
      // set our baseURL reference path
      System.config({
        baseURL: '/app',
        // or 'traceur' or 'typescript'
        transpiler: 'babel',
        // or traceurOptions or typescriptOptions
        babelOptions: {

        }
      });

      // loads /app/main.js
      System.import('main.js');
    </script>

在System.js运行中完成所有工作，在生产模式时使用ES6模块通常要交给转换器完成。如果没有在生产模式，System.js可以唤起转换器，在生产和调试环境中无缝转换。

## 总结

在过去建造模块和解决依赖是个让人头疼的事。

现在库的形式和ES2015模块解决了大部分问题。如果你开始了新的项目，用ES6吧，它会一直坚定的和你走下去，就算现在需要一些其他工具，但是你一定要相信它。可是，如果你坚持ES5，那么让AMD和CommonJS／Node站好队，他们有自己的方向，路要一直走下去，虽然不会有交集，但是不会相隔太远，总能相望。（咦，怎么画风突变，一定是我听的燃系日语歌的锅。）

