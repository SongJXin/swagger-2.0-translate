# Basic Authentication

[基本身份验证](https://en.wikipedia.org/wiki/Basic_access_authentication)是一种非常简单的身份验证方案，内置于HTTP协议中。客户端使用Authorization标头发送HTTP请求，该标头包含`Basic`单词后跟空格和base64编码的`username:password`字符串。例如，包含`demo/ p@55w0rdcredentials` 的标头将编码为：

```YAML
Authorization: Basic ZGVtbzpwQDU1dzByZA==
```

**注意**：由于base64易于解码，因此基本身份验证只能与其他安全机制（如HTTPS / SSL）一起使用。  
基本身份验证很容易定义。在全局`securityDefinitions`部分中，添加一个带有`type: basic`任意名称的条目（在本例中为*basicAuth*）。然后，使用该`security`部分将安全性应用于整个API或特定操作。

```YAML
securityDefinitions:
  basicAuth:
    type: basic
# To apply Basic auth to the whole API:
security:
  - basicAuth: []
paths:
  /something:
    get:
      # To apply Basic auth to an individual operation:
      security:
        - basicAuth: []
      responses:
        200:
          description: OK (successfully authenticated)
```

## 401 Response

您还可以为缺少或不正确凭据的请求定义401“未授权”响应。此响应包括`WWW-Authenticate`标题，您可能需要提及。与其他常见响应一样，401响应可以在全局`responses`部分中定义，并从多个操作中引用。

```YAML
paths:
  /something:
    get:
      ...
      responses:
        ...
        401:
           $ref: '#/responses/UnauthorizedError'
    post:
      ...
      responses:
        ...
        401:
          $ref: '#/responses/UnauthorizedError'
responses:
  UnauthorizedError:
    description: Authentication information is missing or invalid
    headers:
      WWW_Authenticate:
        type: string
```