---
title: 搬运一些ES6内容，以及在ES5中的写法
date: 2017-10-13 15:48:19
tags: 前端
---
## 作用域

### 不可变变量

>只可以使变量本身不可以写，并非它分配的内容，比如如果内容是一个对象，意味着对象依然可以修改。

ES 5

    const PI = 3.141593
	PI > 3.0
	
ES 6
	
	Object.defineProperty(typeof global === "object" ? 	global : window, "PI", {
       value:        3.141593,
       enumerable:   true,
       writable:     false,
   	   configurable: false
	})
	PI > 3.0;
	
### 块级作用域变量

> 块级作用域的变量或常量不提升

ES 6

    for (let i = 0; i < a.length; i++) {
        let x = a[i]
        …
    }
    for (let i = 0; i < b.length; i++) {
        let y = b[i]
        …
    }

    let callbacks = []
    for (let i = 0; i <= 2; i++) {
        callbacks[i] = function () { return i * 2 }
    }
    callbacks[0]() === 0
    callbacks[1]() === 2
    callbacks[2]() === 4

ES 5

    var i, x, y;
    for (i = 0; i < a.length; i++) {
        x = a[i];
        …
    }
    for (i = 0; i < b.length; i++) {
        y = b[i];
        …
    }

    var callbacks = [];
    for (var i = 0; i <= 2; i++) {
        (function (i) {
            callbacks[i] = function() { return i * 2; };
        })(i);
    }
    callbacks[0]() === 0;
    callbacks[1]() === 2;
    callbacks[2]() === 4;
    
### 块级作用域函数

> 定义块级作用域函数

ES 6

    {
        function foo () { return 1 }
        foo() === 1
        {
            function foo () { return 2 }
            foo() === 2
        }
        foo() === 1
    }
   
ES 5

    (function () {
        var foo = function () { return 1; }
        foo() === 1;
        (function () {
            var foo = function () { return 2; }
            foo() === 2;
        })();
        foo() === 1;
    })();
   
## 箭头函数

### 表达体

> 更好的闭合函数 

ES 6

    odds  = evens.map(v => v + 1)
    pairs = evens.map(v => ({ even: v, odd: v + 1 }))
    nums  = evens.map((v, i) => v + i)

ES 5
    
    odds  = evens.map(function (v) { return v + 1; });
    pairs = evens.map(function (v) { return { even: v, odd: v + 1 }; });
    nums  = evens.map(function (v, i) { return v + i; });
    
### 声明

> 同上
 
ES 6

    nums.forEach(v => {
  	 if (v % 5 === 0)
       fives.push(v)
	 })

ES 5 
    
    nums.forEach(function (v) {
    if (v % 5 === 0)
       fives.push(v);
    });
        
### This

> 更直观的处理当前对象上下文

ES 6

    this.nums.forEach((v) => {
    if (v % 5 === 0)
        this.fives.push(v)
    })
    
ES 5

    //  variant 1
    var self = this;
    this.nums.forEach(function (v) {
        if (v % 5 === 0)
            self.fives.push(v);
    });

    //  variant 2
    this.nums.forEach(function (v) {
        if (v % 5 === 0)
            this.fives.push(v);
    }, this);

    //  variant 3 (since ECMAScript 5.1 only)
    this.nums.forEach(function (v) {
        if (v % 5 === 0)
            this.fives.push(v);
    }.bind(this));

## 扩展的参数处理

### 默认参数值

> 简单直观的函数默认值

ES 6
    
    function f (x, y = 7, z = 42) {
        return x + y + z
    }
    f(1) === 50
    
ES 5

    function f (x, y, z) {
    if (y === undefined)
        y = 7;
    if (z === undefined)
        z = 42;
    return x + y + z;
    }; 
    f(1) === 50;

### 剩余参数

> 汇总可变函数参数到一个参数内

ES 6

    function f (x, y, ...a) {
        return (x + y) * a.length
    }
    f(1, 2, "hello", true, 7) === 9

ES 5

    function f (x, y) {
    var a = Array.prototype.slice.call(arguments, 2);
        return (x + y) * a.length;
    };
    f(1, 2, "hello", true, 7) === 9;
    
### 传播

>可以传递可迭代集合里的元素到字面元素或者函数参数里

ES 6

    var params = [ "hello", true, 7 ]
    var other = [ 1, 2, ...params ] // [ 1, 2, "hello", true, 7 ]

    function f (x, y, ...a) {
        return (x + y) * a.length
    }
    f(1, 2, ...params) === 9

    var str = "foo"
    var chars = [ ...str ] // [ "f", "o", "o" ]

ES 5

    var params = [ "hello", true, 7 ];
    var other = [ 1, 2 ].concat(params); // [ 1, 2, "hello", true, 7 ]

    function f (x, y) {
        var a = Array.prototype.slice.call(arguments, 2);
        return (x + y) * a.length;
    };
    f.apply(undefined, [ 1, 2 ].concat(params)) === 9;

    var str = "foo";
    var chars = str.split(""); // [ "f", "o", "o" ]

## 模版语法

### 字符串插值

> 单行或者多行直观的表达插值 

ES 6

    var customer = { name: "Foo" }
    var card = { amount: 7, product: "Bar", unitprice: 42 }
    var message = `Hello ${customer.name},
    want to buy ${card.amount} ${card.product} for
    a total of ${card.amount * card.unitprice} bucks?`
    
ES 5

    var customer = { name: "Foo" };
    var card = { amount: 7, product: "Bar", unitprice: 42 };
    var message = "Hello " + customer.name + ",\n" +
    "want to buy " + card.amount + " " + card.product + " for\n" +
    "a total of " + (card.amount * card.unitprice) + " bucks?";

### 自定义插值

> 灵活的表达插值

ES 6

    get`http://example.com/foo?bar=${bar + baz}&quux=${quux}`

ES 5

    get([ "http://example.com/foo?bar=", "&quux=", "" ],bar + baz, quux);
    
### 原生字符串访问

> 访问原生模版字符串内容（反斜杠不会被解释）

    function quux (strings, ...values) {
        strings[0] === "foo\n"
        strings[1] === "bar"
        strings.raw[0] === "foo\\n"
        strings.raw[1] === "bar"
        values[0] === 42
    }
    quux `foo\n${ 42 }bar`

    String.raw `foo\n${ 42 }bar` === "foo\\n42bar"

## 扩展文字
### 二进制和八进制

> 直接支持二进制和八进制内容

ES 6 
    
    0b111110111 === 503
    0o767 === 503

ES 5

    parseInt("111110111", 2) === 503;
    parseInt("767", 8) === 503;
    0767 === 503; // only in non-strict, backward compatibility mode

### unicode和正则表达式

> 扩展支持字符串使用unicode和正则表达式

ES 6

    "𠮷".length === 2
    "𠮷".match(/./u)[0].length === 2
    "𠮷" === "\uD842\uDFB7" 
    "𠮷" === "\u{20BB7}"
    "𠮷".codePointAt(0) == 0x20BB7
    for (let codepoint of "𠮷") console.log(codepoint)

ES 5 

    "𠮷".length === 2;
    "𠮷".match(/(?:[\0-\t\x0B\f\x0E-\u2027\u202A-\uD7FF\uE000- \uFFFF][\uD800-\uDBFF][\uDC00-\uDFFF][\uD800-\uDBFF](?![\uDC00-\uDFFF])(?:[^\uD800-\uDBFF]^)[\uDC00-\uDFFF])/)[0].length === 2;
    "𠮷" === "\uD842\uDFB7";
    //  no equivalent in ES5
    //  no equivalent in ES5
    //  no equivalent in ES5
    
## 增强的正则表达式

### 正则表达式粘性匹配

> 保持匹配位置在匹配内容之间，支持任意长度字符串高效解析，即使有任意数量的不同正则表达式。

ES 6

    let parser = (input, match) => {
    for (let pos = 0, lastPos = input.length; pos < lastPos; ) {
        for (let i = 0; i < match.length; i++) {
            match[i].pattern.lastIndex = pos
            let found
            if ((found = match[i].pattern.exec(input)) !== null) {
                match[i].action(found)
                pos = match[i].pattern.lastIndex
                break
            }
        }
      }
    }

    let report = (match) => {
        console.log(JSON.stringify(match))
    }
    parser("Foo 1 Bar 7 Baz 42", [
        { pattern: /^Foo\s+(\d+)/y, action: (match) => report(match) },
        { pattern: /^Bar\s+(\d+)/y, action: (match) => report(match) },
        { pattern: /^Baz\s+(\d+)/y, action: (match) => report(match) },
        { pattern: /^\s*/y,         action: (match) => {}            }
    ])

ES 5

    var parser = function (input, match) {
    for (var i, found, inputTmp = input; inputTmp !== ""; ) {
        for (i = 0; i < match.length; i++) {
            if ((found = match[i].pattern.exec(inputTmp)) !== null) {
                match[i].action(found);
                inputTmp = inputTmp.substr(found[0].length);
                break;
            }
        }
      }
    }

    var report = function (match) {
        console.log(JSON.stringify(match));
    };
    parser("Foo 1 Bar 7 Baz 42", [
        { pattern: /^Foo\s+(\d+)/, action: function (match)    { report(match); } },
        { pattern: /^Bar\s+(\d+)/, action: function (match) { report(match); } },
        { pattern: /^Baz\s+(\d+)/, action: function (match) { report(match); } },
        { pattern: /^\s*/,         action: function (match) {}                 }
    ]); 

## 增强的对象属性

### 属性速记

> 更短的语法，用于公共对象属性定义

ES 6
    
    obj = { x, y }
    
ES 5

    obj = { x: x, y: y };
    

### 计算属性名 

> 计算属性定义支持计算命名 
 
ES 6 
    
    let obj = {
        foo: "bar",
        [ "baz" + quux() ]: 42
    }

ES 5

    var obj = {
        foo: "bar"
    };
    obj[ "baz" + quux() ] = 42;

### 方法属性

> 对象属性定义支持方法符号 

ES 6 

    obj = {
        foo (a, b) {
            …
        },
        bar (x, y) {
            …
        },
        *quux (x, y) {
            …
        }
    }
    
ES 5 

    obj = {
        foo: function (a, b) {
            …
        },
        bar: function (x, y) {
            …
        },
        //  quux: no equivalent in ES5
        …
    };

## 解构 

### 数组匹配 

> 直观且灵活的将数组解构为单个变量

ES 6 

    var list = [ 1, 2, 3 ]
    var [ a, , b ] = list
    [ b, a ] = [ a, b ]

ES 5 
    
    var list = [ 1, 2, 3 ];
    var a = list[0], b = list[2];
    var tmp = a; a = b; b = tmp;

### 对象匹配，速记符号 

> 同上

ES 6 

    var { op, lhs, rhs } = getASTNode()
    
ES 5 

    var tmp = getASTNode();
    var op  = tmp.op;
    var lhs = tmp.lhs;
    var rhs = tmp.rhs;

### 对象匹配 深匹配 

> 同上 

ES 6 

    var { op: a, lhs: { op: b }, rhs: c } = getASTNode()

ES 5 

    var tmp = getASTNode();
    var a = tmp.op;
    var b = tmp.lhs.op;
    var c = tmp.rhs;

### 对象数组匹配，默认值 

> 为对象和数组解构简单直观的默认值

ES 6 

    var obj = { a: 1 }
    var list = [ 1 ]
    var { a, b = 2 } = obj
    var [ x, y = 2 ] = list
 
ES 5

    var obj = { a: 1 };
    var list = [ 1 ];
    var a = obj.a;
    var b = obj.b === undefined ? 2 : obj.b;
    var x = list[0];
    var y = list[1] === undefined ? 2 : list[1];
    
### 参数上下文匹配 

> 函数调用期间将数组和对象简单灵活的解构为独立参数

ES 6 

    function f ([ name, val ]) {
        console.log(name, val)
    }
    function g ({ name: n, val: v }) {
        console.log(n, v)
    }
    function h ({ name, val }) {
        console.log(name, val)
    }
    f([ "bar", 42 ])
    g({ name: "foo", val:  7 })
    h({ name: "bar", val: 42 })
 
ES 5

    function f (arg) {
        var name = arg[0];
        var val  = arg[1];
        console.log(name, val);
    };
    function g (arg) {
        var n = arg.name;
        var v = arg.val;
        console.log(n, v);
    };
    function h (arg) {
        var name = arg.name;
        var val  = arg.val;
        console.log(name, val);
    };
    f([ "bar", 42 ]);
    g({ name: "foo", val:  7 });
    h({ name: "bar", val: 42 });

### 失败弱处理

> 可选默认值 

ES 6 

    var list = [ 7, 42 ]
    var [ a = 1, b = 2, c = 3, d ] = list
    a === 7
    b === 42
    c === 3
    d === undefined

ES 5 

    var list = [ 7, 42 ];
    var a = typeof list[0] !== "undefined" ? list[0] : 1;
    var b = typeof list[1] !== "undefined" ? list[1] : 2;
    var c = typeof list[2] !== "undefined" ? list[2] : 3;
    var d = typeof list[3] !== "undefined" ? list[3] : undefined;
    a === 7;
    b === 42;
    c === 3;
    d === undefined;
    
## 模块 

### 值导出导入 

> 支持没有全局变量污染的模块导入导出 

ES 6 

    //  lib/math.js
    export function sum (x, y) { return x + y }
    export var pi = 3.141593

    //  someApp.js
    import * as math from "lib/math"
    console.log("2π = " + math.sum(math.pi, math.pi))

    //  otherApp.js
    import { sum, pi } from "lib/math"
    console.log("2π = " + sum(pi, pi)) 

ES 5 

    //  lib/math.js
    LibMath = {};
    LibMath.sum = function (x, y) { return x + y };
    LibMath.pi = 3.141593;

    //  someApp.js
    var math = LibMath;
    console.log("2π = " + math.sum(math.pi, math.pi));

    //  otherApp.js
    var sum = LibMath.sum, pi = LibMath.pi;
    console.log("2π = " + sum(pi, pi));
    
### 默认值和通配符

> 使值成为默认导出值，混合值 

ES 6 

    //  lib/mathplusplus.js
    export * from "lib/math"
    export var e = 2.71828182846
    export default (x) => Math.exp(x)

    //  someApp.js
    import exp, { pi, e } from "lib/mathplusplus"
    console.log("e^{π} = " + exp(pi))

ES 5 

    //  lib/mathplusplus.js
    LibMathPP = {};
    for (symbol in LibMath)
       if (LibMath.hasOwnProperty(symbol))
           LibMathPP[symbol] = LibMath[symbol];
    LibMathPP.e = 2.71828182846;
    LibMathPP.exp = function (x) { return Math.exp(x) };

    //  someApp.js
    var exp = LibMathPP.exp, pi = LibMathPP.pi, e = LibMathPP.e;
    console.log("e^{π} = " + exp(pi));

## 类 

### 类 定义 

> 更加直观,OOP式和无需样板的类 

ES 6

    class Shape {
        constructor (id, x, y) {
            this.id = id
            this.move(x, y)
        }
        move (x, y) {
            this.x = x
            this.y = y
        }
    }

ES 5 

    var Shape = function (id, x, y) {
        this.id = id;
        this.move(x, y);
    };
    Shape.prototype.move = function (x, y) {
       this.x = x;
       this.y = y;
    };

### 类继承

> 同上 

ES 6 

    class Rectangle extends Shape {
        constructor (id, x, y, width, height) {
            super(id, x, y)
            this.width  = width
            this.height = height
        }
    }
    class Circle extends Shape {
        constructor (id, x, y, radius) {
            super(id, x, y)
            this.radius = radius
        }
    }

ES 5

    var Rectangle = function (id, x, y, width, height) {
        Shape.call(this, id, x, y);
        this.width  = width;
        this.height = height;
    };
    Rectangle.prototype = Object.create(Shape.prototype);
    Rectangle.prototype.constructor = Rectangle;
    var Circle = function (id, x, y, radius) {
        Shape.call(this, id, x, y);
        this.radius = radius;
    };
    Circle.prototype = Object.create(Shape.prototype);
    Circle.prototype.constructor = Circle;  

### 从表达式继承类 

> 支持通过扩展表达式函数的 mixin-style 插值

ES 6 

    var aggregation = (baseClass, ...mixins) => {
    let base = class _Combined extends baseClass {
        constructor (...args) {
            super(...args)
            mixins.forEach((mixin) => {
                mixin.prototype.initializer.call(this)
            })
        }
    }
    let copyProps = (target, source) => {
        Object.getOwnPropertyNames(source)
            .concat(Object.getOwnPropertySymbols(source))
            .forEach((prop) => {
            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
                return
            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
        })
    }
    mixins.forEach((mixin) => {
            copyProps(base.prototype, mixin.prototype)
            copyProps(base, mixin)
        })
        return base
    }

    class Colored {
        initializer ()     { this._color = "white" }
        get color ()       { return this._color }
        set color (v)      { this._color = v }
    }

    class ZCoord {
        initializer ()     { this._z = 0 }
        get z ()           { return this._z }
        set z (v)          { this._z = v }
    }

    class Shape {
        constructor (x, y) { this._x = x; this._y = y }
        get x ()           { return this._x }
        set x (v)          { this._x = v }
        get y ()           { return this._y }
        set y (v)          { this._y = v }
    }

    class Rectangle extends aggregation(Shape, Colored, ZCoord) {}

    var rect = new Rectangle(7, 42)
    rect.z     = 1000
    rect.color = "red"
    console.log(rect.x, rect.y, rect.z, rect.color) 

ES 5

    var aggregation = function (baseClass, mixins) {
    var base = function () {
        baseClass.apply(this, arguments);
        mixins.forEach(function (mixin) {
            mixin.prototype.initializer.call(this);
        }.bind(this));
    };
    base.prototype = Object.create(baseClass.prototype);
    base.prototype.constructor = base;
    var copyProps = function (target, source) {
        Object.getOwnPropertyNames(source).forEach(function (prop) {
            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
                return
            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
        })
    }
    mixins.forEach(function (mixin) {
            copyProps(base.prototype, mixin.prototype);
            copyProps(base, mixin);
        });
        return base;
    };

    var Colored = function () {};
    Colored.prototype = {
        initializer: function ()  { this._color = "white"; },
        getColor:    function ()  { return this._color; },
        setColor:    function (v) { this._color = v; }
    };

    var ZCoord = function () {};
    ZCoord.prototype = {
        initializer: function ()  { this._z = 0; },
        getZ:        function ()  { return this._z; },
        setZ:        function (v) { this._z = v; }
    };

    var Shape = function (x, y) {
        this._x = x; this._y = y;
    };
    Shape.prototype = {
        getX: function ()  { return this._x; },
        setX: function (v) { this._x = v; },
        getY: function ()  { return this._y; },
        setY: function (v) { this._y = v; }
    }

    var _Combined = aggregation(Shape, [ Colored, ZCoord ]);
    var Rectangle = function (x, y) {
        _Combined.call(this, x, y);
    };
    Rectangle.prototype = Object.create(_Combined.prototype);
    Rectangle.prototype.constructor = Rectangle;

    var rect = new Rectangle(7, 42);
    rect.setZ(1000);
    rect.setColor("red");
    console.log(rect.getX(), rect.getY(), rect.getZ(), rect.getColor());
    
### 基本的类访问 

> 类构造器和方法的简单访问 

ES 6 

    class Shape {
        …
        toString () {
             return `Shape(${this.id})`
        }
    }
    class Rectangle extends Shape {
        constructor (id, x, y, width, height) {
            super(id, x, y)
            …
        } 
        toString () {
            return "Rectangle > " + super.toString()
        }
    }
    class Circle extends Shape {
        constructor (id, x, y, radius) {
            super(id, x, y)
            …
        }
        toString () {
            return "Circle > " + super.toString()
        }
 ES 5
 
    var Shape = function (id, x, y) {
        …
    };
    Shape.prototype.toString = function (x, y) {
        return "Shape(" + this.id + ")"
    };
    var Rectangle = function (id, x, y, width, height) {
        Shape.call(this, id, x, y);
        …
    };
    Rectangle.prototype.toString = function () {
        return "Rectangle > " + Shape.prototype.toString.call(this);
    };
    var Circle = function (id, x, y, radius) {
        Shape.call(this, id, x, y);
        …
    };
    Circle.prototype.toString = function () {
        return "Circle > " + Shape.prototype.toString.call(this);
    };


### 静态成员 

> 简单支持静态类成员 

ES 6 

    class Rectangle extends Shape {
    	…
    	static defaultRectangle () {
       	 return new Rectangle("default", 0, 0, 100, 100)
    	}
	}
	class Circle extends Shape {
   		 …
    	 static defaultCircle () {
        	return new Circle("default", 0, 0, 100)
    	 }
	}
	var defRectangle = Rectangle.defaultRectangle()
	var defCircle    = Circle.defaultCircle()

ES 5 
    
    var Rectangle = function (id, x, y, width, height) {
    	…
	};
	Rectangle.defaultRectangle = function () {
		return new Rectangle("default", 0, 0, 100, 100);
    };
    var Circle = function (id, x, y, width, height) {
        …
    };
    Circle.defaultCircle = function () {
        return new Circle("default", 0, 0, 100);
    };
	var defRectangle = Rectangle.defaultRectangle();
	var defCircle    = Circle.defaultCircle();

### Getter/Seeter 

> 类也可以使用Getter/Setter 

ES 6

    class Rectangle {
        constructor (width, height) {
        	this._width  = width
        	this._height = height
    	}
    	set width  (width)  { this._width = width               }
    	get width  ()       { return this._width                }
    	set height (height) { this._height = height             }
    	get height ()       { return this._height               }
    	get area   ()       { return this._width * this._height }
	}
	var r = new Rectangle(50, 20)
	r.area === 1000
    
ES 5 
    
    var Rectangle = function (width, height) {
    	this._width  = width;
    	this._height = height;
	};
	Rectangle.prototype = {
    	set width  (width)  { this._width = width;               },
    	get width  ()       { return this._width;                },
    	set height (height) { this._height = height;             },
    	get height ()       { return this._height;               },
    	get area   ()       { return this._width * this._height; }
	};
	var r = new Rectangle(50, 20);
	r.area === 1000;
    
## 标记类型 

### 标记类型 

> 为对象属性识别使用独一无二的数据类型。标记有可选的描述，但是只适用于调试。 

    Symbol("foo") !== Symbol("foo")
	const foo = Symbol()
	const bar = Symbol()
	typeof foo === "symbol"
	typeof bar === "symbol"
	let obj = {}
	obj[foo] = "foo"
	obj[bar] = "bar"
	JSON.stringify(obj) // {}
	Object.keys(obj) // []
	Object.getOwnPropertyNames(obj) // []
	Object.getOwnPropertySymbols(obj) // [ foo, bar ]
    
### 全局标记 

> 通过唯一的键索引 

    Symbol.for("app.foo") === Symbol.for("app.foo")
	const foo = Symbol.for("app.foo")
	const bar = Symbol.for("app.bar")
	Symbol.keyFor(foo) === "app.foo"
	Symbol.keyFor(bar) === "app.bar"
	typeof foo === "symbol"
	typeof bar === "symbol"
	let obj = {}
	obj[foo] = "foo"
	obj[bar] = "bar"
	JSON.stringify(obj) // {}
	Object.keys(obj) // []
	Object.getOwnPropertyNames(obj) // []
	Object.getOwnPropertySymbols(obj) // [ foo, bar ]
    
## 迭代器 
### 迭代器和 For-Of 操作
> 支持迭代协议允许对象定义自己的迭代行为。另外，支持迭代器协议生成值的序列（无论有限还是无限）。最后提供方便的迭代方式迭代可迭代对象。 

ES 6 
    
    let fibonacci = {
    [Symbol.iterator]() {
        let pre = 0, cur = 1
        return {
           next () {
               [ pre, cur ] = [ cur, pre + cur ]
               return { done: false, value: cur }
          	 }
       	 }
   		}
	}

    for (let n of fibonacci) {
    	if (n > 1000)
       	 break
    	console.log(n)
	} 

ES 5
    
    var fibonacci = {
    next: (function () {
        var pre = 0, cur = 1;
        return function () {
            tmp = pre;
            pre = cur;
            cur += tmp;
            return cur;
       	 };
   		})()
	};

	var n;
	for (;;) {
   		n = fibonacci.next();
    	if (n > 1000)
           break;
    	console.log(n);
	} 

## Promises 

### Promises 使用 

ES 6 

    function msgAfterTimeout (msg, who, timeout) {
        return new Promise((resolve, reject) => {
            setTimeout(() => resolve(`${msg} Hello ${who}!`), timeout)
        })
    }
	 msgAfterTimeout("", "Foo", 100).then((msg) =>
   	     msgAfterTimeout(msg, "Bar", 200)
    ).then((msg) => {
        console.log(`done after 300ms:${msg}`)
    }) 

ES 5
    
    function msgAfterTimeout (msg, who, timeout, onDone) {
        setTimeout(function () {
             onDone(msg + " Hello " + who + "!");
        }, timeout);
	}
    msgAfterTimeout("", "Foo", 100, function (msg) {
        msgAfterTimeout(msg, "Bar", 200, function (msg) {
            console.log("done after 300ms:" + msg);
        });
    });

### Promises 合并 

> 合并一个或者多个Promises不用担心异步问题。 

ES 6 

    function fetchAsync (url, timeout, onData, onError) {
        …
    } 
    let fetchPromised = (url, timeout) => {
        return new Promise((resolve, reject) => {
            fetchAsync(url, timeout, resolve, reject)
        })
    }
    Promise.all([
        fetchPromised("http://backend/foo.txt", 500),
        fetchPromised("http://backend/bar.txt", 500),
        fetchPromised("http://backend/baz.txt", 500)
    ]).then((data) => {
        let [ foo, bar, baz ] = data
        console.log(`success: foo=${foo} bar=${bar} baz=${baz}`)
    }, (err) => {
        console.log(`error: ${err}`)
    })

ES 5
    
    function fetchAsync (url, timeout, onData, onError) {
        …
	 }
    function fetchAll (request, onData, onError) {
    var result = [], results = 0;
    for (var i = 0; i < request.length; i++) {
        result[i] = null;
        (function (i) {
                fetchAsync(request[i].url, request[i].timeout, function (data) {
                    result[i] = data;
                    if (++results === request.length)
                        onData(result);
                }, onError);
            })(i);
        }
    }
    fetchAll([
    { url: "http://backend/foo.txt", timeout: 500 },
    { url: "http://backend/bar.txt", timeout: 500 },
    { url: "http://backend/baz.txt", timeout: 500 }
    ], function (data) {
    var foo = data[0], bar = data[1], baz = data[2];
    console.log("success: foo=" + foo + " bar=" + bar + " baz=" + baz);
    }, function (err) {
        console.log("error: " + err);
    });
    
    
    
    
    
    
    
    
    
    
    
    