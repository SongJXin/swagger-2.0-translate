# API Host and Base URL

REST api 有一个附加端点路径的基本 URL 。基本 URL 由 API 规范的根级别`scheme`、`host`和 `basePath`定义。

```YAML
host: petstore.swagger.io
basePath: /v2
schemes:
  - https
```

所有 API 路径都相对于这个基本URL，例如，*/users* 实际上是指 `<scheme>://<host>/<basePath>/users`
![ ](https://swagger.io/swagger/media/Images/url-structure.png)

## schemes

`schemes`是API使用的传输协议。Swagger 支持`http`、`https`和[WebSocket](https://en.wikipedia.org/wiki/WebSocket)模式——`ws`和`wss`。与YAML中的任何列表一样，可以使用列表语法指定方案:

```YAML
schemes:
  - http
  - https
```

或逐字数组语法:

```YAML
schemes: [http, https]
```

如果没有指定`schemes`，则用于服务 API 规范的模式将用于API调用。

## host

`host`是提供 API 的主机的域名或IP地址(IPv4)。如果与方案的默认端口(HTTP为80,HTTPS为443)不同，则可以包含端口号。注意，这必须是唯一的主机，没有http(s)://或子路径。例如：

```YAML
api.example.com
example.com:8089
93.184.216.34
93.184.216.34:8089
```

错误的：

```YAML
http://api.example.com
example.com/api/v1
```

如果没有指定`host`，则假定它与提供API文档的主机相同。

## basePath

`basePath`是所有 API 路径的 URL 前缀，相对于主机根目录。它必须以斜杠`/`开头。如果没有指定`basePath`，它默认为`/`，也就是说，所有路径都从主机根目录开始。有效的基本路径:

```YAML
/v2
/api/v2
/
```

错误的：

```YAML
v2
```

## 省略 host 和 scheme

为了实现更动态的关联，可以省略主机和scheme。在这种情况下，用于服务 API 文档的主机和方案将用于 API 调用。例如，如果基于Swagger ui 的文档托管在`https://api.example.com/apidocs/index.html`中，“try it out”API调用将定向到`https://api.example.com`。

## FAQ

### 我可以指定多个主机，例如开发、测试和生产吗

OpenAPI 3.0支持多个主机。每个API规范只支持一个主机(如果将 HTTP 和 HTTPS 算作不同的主机，则支持两个主机)。针对多个主机的一种可能方法是，从规范中省略`host`和`scheme`，并从每个主机提供它。在这种情况下，规范的每个副本都将针对相应的主机。

### 主机和basePath支持模板吗?如

```YAML
https://{customer_id}.saas-app.com/api/v1
https://api.saas-app.com/v1/{customer_id}/apis
```

OpenAPI 3.0支持这一点，但2.0不支持。有关主机模板的解决方案，请参阅前面的问题。

### 我可以为 HTTP 和 HTTPS 指定不同的端口吗?如

```YAML
http://example.com:8080
https://example.com:8443
```

OpenAPI 3.0支持这一点，但2.0不支持。在2.0中，您可以省略`host`和`scheme`，并从两台主机提供规范。这样，规范的每个副本都将针对用于访问该规范的主机和端口。