---
title: https发送post请求以及nodejs处理post
date: 2017-09-14 13:45:23
tags:
---
### 客户端发送post请求：

    sendPost: function(){
				var querystring = require('querystring');
				var httpRequest;

				var bodyString = {
					name: 'me',
					id: 2223,
					password: 'indinf'
				};

				var postStr = querystring.stringify(bodyString);

				httpRequest = new XMLHttpRequest();
				if(!httpRequest) {
					alert('这里有一点错误！');
					return false;
				}
				httpRequest.onreadystatechange = function(){
					if(httpRequest.readyState === XMLHttpRequest.DONE){
						if(httpRequest.status === 200) {
							alert(httpRequest.responseText);
						}else{
							alert('呀！网络错误！');
						}
					}
				};
		
				httpRequest.open('POST','https://example.com/');
				httpRequest.setRequestHeader("Content-type","application/x-www-form-urlencoded");
				httpRequest.send(postStr);
	}
	
### Node.js端接收处理post请求

    	const https = require('https');
		const fs = require('fs');

		const options = {
			key: fs.readFileSync('key/example.key'), //这里是你的key和证书的位置
			cert: fs.readFileSync('key/example.crt')
		};

		https.createServer(options,(req, res) => {
		res.setHeader('Access-Control-Allow-Origin','*');

		if(req.method == 'POST') {
			console.log("POST");
			var body = "";
			req.on('data', function(data) {
				body += data;
			})
			req.on('end', function(){
				console.log(body);
			})
			res.writeHead(200,{'Content-Type': 'text/html'});
			res.end('post received');
		}else{
			console.log('GET');
			res.writeHead(200,{'Content-Type':'text/html'});
			res.end('hello');
		}

		}).listen(2323);

		console.log('Server running at https://www.example.cn:2323/');
		

