---
title: ccap安装失败
date: 2017-09-13 13:59:07
tags:
---
ccap是一个nodejs用的验证码模块，centos7下安装报错

    `sh "-c" "node-gyp rebuild"` failed

仔细看了下依赖

    npm install ccap (new version, need node 0.12.x and 4.x.x and 6.x.x, on linux need gcc v4.8+)
    
原来需要gcc

    sudo yum install gcc gcc-c++
    
之后安装正确版本nodejs

然后安装node-gyp

    cnpm install node-gyp -g
    
ccap

	cnpm install ccap
	
版本匹配很重要