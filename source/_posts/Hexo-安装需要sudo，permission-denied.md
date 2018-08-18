---
title: Hexo 安装需要sudo，permission denied
date: 2017-09-08 18:39:55
tags:
---
环境是 MacBook Sierra 10.12.6 
因为之前安装好了node和git，并且一直在使用，安装方式是官网下的pkg，所以跳过Hexo文档里前面的部分直接安装：
    	
    	npm install -g hexo-cli

但是会出现premission问题，按照常规方法sudo解决。虽然安装成功，但是出现了可怕的问题，所有跟Hexo有关的操作都必须要用root权限。

后来谷歌了下，原因应该出在了npm上，权限问题的源头。最彻底的解决办法是彻底删除node和npm，全新安装。

    	sudo rm -rf /usr/local/{lib/node{,/.npm,_modules},bin,share/man}/{npm*,node*,man1/node*}
    	sudo rm -rf /usr/local/bin/npm /usr/local/share/man/man1/node* /usr/local/lib/dtrace/node.d ~/.npm ~/.node-gyp /opt/local/bin/node opt/local/include/node /opt/local/lib/node_modules
    	
以上两行命令彻底删除node及npm，然后根据Hexo官网安装node：
    	
    	curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
    	nvm install stable
以及Hexo：

    	npm install -g hexo-cli

问题解决。


    	

