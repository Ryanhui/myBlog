---
title: 跨域访问中的withCredentials
date: 2017-12-02 14:23:23
tags: 前端
---

XMLHttpRequest.withCredentials属性是一个布尔值，用来表明在进行跨站的Access-Control 请求时是否使用证书类信息，例如cookies，授权的头文件或者TSL客户端证书。在同一个站点下使用withCredentials属性是无效的。

如果在发送来自其他域的XMLHttpRequest请求之前，未设置withCredentials 为true，那么就不能为它自己的域设置cookie值。而通过设置withCredentials 为true获得的第三方cookies，将会依旧享受同源策略，因此不能被通过document.cookie或者从头部相应请求的脚本等访问。

原生ajax请求中

    var xhr = new XMLHttpRequest();
	xhr.open('GET', 'http://example.com/', true);
	xhr.withCredentials = true;
	xhr.send(null);

在 fetch 方法中，要想发送 Credentials ，则需要设置为“include”

    fetch(url, {
  		credentials: 'include'
	})

在 jquery 的 ajax 中,将 xhrFields 中的 withCredentials 设置为 true

    $.ajax({
    url: a_cross_domain_url,
    xhrFields: {
      		withCredentials: true
   	 	}
	});