---
title: Kong网关入门
date: 2018-09-18 18:10:09
tags: 分享

---

## 前言

一个典型的使用Kong网关的请求遵循以下流程。

![图解](F:\code\myBlog\source\_posts\Kong网关入门\p1.png)

每一个请求API的访问首先会访问Kong，之后被代理到最终的API上。在请求和响应中间，Kong可以根据用户的需要执行很多插件，使其功能更加强大。Kong可以有效的成为每一个API请求的入口。

## 使用

当Kong成功启动以后，默认的，Kong会监听以下端口:

:8000  Kong监听来自HTTP请求的端口，转发到上游服务。
:8443  Kong监听来自HTTPS请求的端口，除了只接受HTTPS请求外，其他行为和8000端口一致，这个端口可以在配置文件中禁用。
:8001  Kong的配置管理接口，即Admin API，通过访问此端口配置Kong。
:8444  Admin API 用来监听HTTPS请求的。

理解8001端口尤为重要，在Kong中，所有的配置都是通过Get、Post、Patch等请求完成的。通过命令行的curl命令，或者postman等其他能发送http请求的工具将需要的配置信息按照Kong的API发送到8001端口即可更改配置，如添加服务，增加API，安装插件等操作。

例如，一个添加服务的命令如下：

```bash
$ curl -i -X POST \
  --url http://localhost:8001/services/ \
  --data 'name=example-service' \
--data 'url=http://mockbin.org'

```

#### 快速使用

这一节，我们要添加一个API到Kong上，为了实现这个目的，我们首先要添加一个服务名称，Kong用这个名称来指代它管理的上游API和微服务。

##### 1.使用Admin API 添加服务

```bash
$ curl -i -X POST \
  --url http://localhost:8001/services/ \
  --data 'name=example-service' \
--data 'url=http://mockbin.org'

```

如果命令正确，将会打印如下结果

```bash
HTTP/1.1 201 Created
Content-Type: application/json
Connection: keep-alive

{
   "host":"mockbin.org",
   "created_at":1519130509,
   "connect_timeout":60000,
   "id":"92956672-f5ea-4e9a-b096-667bf55bc40c",
   "protocol":"http",
   "name":"example-service",
   "read_timeout":60000,
   "port":80,
   "path":null,
   "updated_at":1519130509,
   "retries":5,
   "write_timeout":60000
}

```

##### 2.为服务添加路由

hosts[]=example.com 代表来自example.com主机的请求会被转发到此路由。

```bash
$ curl -i -X POST \
  --url http://localhost:8001/services/example-service/routes \
--data 'hosts[]=example.com'

```

##### 如果命令正确，会打印如下结果

```bash
HTTP/1.1 201 Created
Content-Type: application/json
Connection: keep-alive

{
   "created_at":1519131139,
   "strip_path":true,
   "hosts":[
      "example.com"
   ],
   "preserve_host":false,
   "regex_priority":0,
   "updated_at":1519131139,
   "paths":null,
   "service":{
      "id":"79d7ee6e-9fc7-4b95-aa3b-61d2e17e7516"
   },
   "methods":null,
   "protocols":[
      "http",
      "https"
   ],
   "id":"f9ce2ed7-c06e-4e16-bd5d-3a82daef3f9d"
}

```

##### 3.测试请求是否被成功从Kong转发到上游服务

以下命令模拟一个get请求到Kong，如果配置正确，Kong应该将请求转发到<http://mockbin.org>，如果你还记得，这是我们在创建服务的时候指定的上游服务。

```bash
$ curl -i -X GET \
  --url http://localhost:8000/ \
--header 'Host: example.com'

```

至此，我们已经建立了一个最简单的Kong实例，监听来自8000端口的请求，如果请求的header为example.com，Kong会转发到mockbin.org，再把响应返回给我们。

## 启用插件

这里，我们要配置Kong插件，Kong的一个核心原则是通过插件扩展功能。

接下来我们配置key-auth插件，用来进行身份验证。在添加此插件以前，所有的请求都会被代理到上游。启用此插件以后，只有携带正确API key的请求会被代理，借此可以保护上游服务。

##### 1.为之前配置的服务增加key-auth插件

输入以下命令

```bash
$ curl -i -X POST \
  --url http://localhost:8001/services/example-service/plugins/ \
--data 'name=key-auth'

```

这个插件也支持config.key_names参数，默认值是['apikey']。这是一个包含apikey的headers和parameters名的列表。

##### 2.	验证插件已经正确配置

```bash
curl -i -X GET \
  --url http://localhost:8000/ \
--header 'Host: example.com'

```

因为还没有指定要求的apikey或者参数，响应应该是401 unauthorized：

```bash
HTTP/1.1 401 Unauthorized
...

{
  "message": "No API key found in request"
}

```

如果想继续正常代理，就要为服务添加用户。

## 添加用户

在此，我们为Kong实例添加一个用户，用户是使用你API的独立个体，可以被追踪，进行访问管理等。

##### 1.	使用RESTful创建用户

假设我们的用户叫Jason

```bash
$ curl -i -X POST \
  --url http://localhost:8001/consumers/ \
--data "username=Jason"

```

你应该看到如下信息

```bash
HTTP/1.1 201 Created
Content-Type: application/json
Connection: keep-alive

{
  "username": "Jason",
  "created_at": 1428555626000,
  "id": "bbdf1c48-19dc-4ab7-cae0-ff4f59d87dc9"
}

```

##### 2.	为你的用户设置key

把ENTER_KEY_HERE替换为想要设置的Key

```bash
$ curl -i -X POST \
  --url http://localhost:8001/consumers/Jason/key-auth/ \
--data 'key=ENTER_KEY_HERE'

```

##### 3.	验证

```bash
$ curl -i -X GET \
  --url http://localhost:8000 \
  --header "Host: example.com" \
--header "apikey: ENTER_KEY_HERE"

```

至此，我们已经完整添加了一个带鉴权的服务。