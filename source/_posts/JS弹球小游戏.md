---
title: JS弹球小游戏
date: 2018-02-01 10:52:58
tags: 前端
---

不知道你有没有玩过这个打砖块的游戏，我模糊记得是在Windows 98上自带的游戏。

在当时的我看来，画面极其科幻，游戏效果无与伦比。

不过我谷歌了一圈也没发现与记忆里一模一样的画面，只找到了这个类似的游戏。

![image](http://ww1.sinaimg.cn/large/6535481cly1fo0xe3iloqj20go09eteh.jpg)

想想那时一个弹球游戏就能玩上整整一天，守着大屁股的显示器，几个伙伴轮流操作，真是让人怀念的年代。

只是当时太过年幼，不知这是何种难得的幸福。

技术发展的脚步飞快，JavaScript似乎已经无所不能，索性就实现了一个类似的小游戏。

虽然物理部分简单，但是既然已有非常完善的物理引擎，何不站在巨人的肩头呢？

## [matter.js](http://brm.io/matter-js/docs/index.html)

一个用JavaScript编写的2D刚体物理引擎。

在引擎中添加世界，在世界中添加物体，启动引擎，一切就动了起来，是不是让人兴奋？

可以添加多个世界，可以添加多个物体，物体可以组合，可以添加事件，可以使用自己的渲染器，可以添加插件，全部随心所欲。

[这里是官方demo](http://brm.io/matter-js/demo/#airFriction)

同所有的引擎一样，在使用前要了解一下概念，比如engine，即引擎。引擎控制着对世界的模拟更新。或者composite，即合成，是物体的集合。一个世界就是一个composite体。等等。

介绍完了matter.js引擎，下面是游戏部分。

为了让游戏变得更有意思一点，我让球球每隔一段时间随机改变方向和速度，带来出其不意的惊喜。

[游戏demo](http://www.boxgame.top/pinball/)

![](http://ww1.sinaimg.cn/large/6535481cly1fo0xekjv13j21e60p1dgm.jpg)

关键

为了让球球持续运动，要使摩擦力，空气摩擦力，静摩擦力设置为0，为了能更好的反弹，要设置restitution（恢复力？）为1，同时密度要合适。

为球体设置的属性这样：

        density: 0.001,
        friction: 0,
        frictionStatic: 0,
        frictionAir: 0,
        restitution: 1

墙体等属性主要是isStatic为true即可，目的是固定在空间上，否则会无限掉落。

对于碰撞事件，我们要进行监听，实现消失，改变颜色，播放音效的功能。

    /* 监控碰撞中事件 */
	 Events.on(engine, 'collisionActive', function(event) {
    	let pairs = event.pairs;

    	for(let i = 0; i < pairs.length; i++) {
        	let pair = pairs[i];
        	pair.bodyA.render.fillStyle = '#42f4b6';
        	pair.bodyB.render.fillStyle = '#41d9f4';
    	}

    	playSound();
	});

操作物体可以在帧前或者帧后，这样才会从下一帧开始应用改变。

    Events.on(engine, 'beforeUpdate', beforeUpdateEvent);
    
    function beforeUpdateEvent(event) {
    /* 球球的位置 */
    var position = ball.position;

    if(event.timestamp % 5100 < 50 && isMovingBall){
        
        var positionX = position.x,
            positionY = position.y;

        /* 如果球球的速度小于13，给球球加速 */
        if(ball.speed < 20) {
            Body.applyForce(ball, {x: positionX, y: positionY}, {x:  Math.random()/100, y: -Math.random()/100}); 
            console.log('speed up');
        }
    }

    /* 球离开区域，游戏失败 */
    if(position.y >= 900) {
        	isMovingBall = false;
        	isOver = true;
        	Composite.remove(world, [box, ball]);
        	Events.off(engine, 'afterUpdate', afterUpdateEvent);
        	Events.off(engine, 'beforeUpdate', beforeUpdateEvent);
  			cover.style.backgroundColor = 'rgba(0,0,0,0.7)';
    	}
    }
   
每次游戏结束为了让物体回归原位，从世界移除物体及监听的事件，重新添加。

这样就完成了一个弹球小游戏。

以上


