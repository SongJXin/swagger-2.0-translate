# API Keys

某些API 使用API​​密钥进行授权。API 密钥是客户端在进行API调用时需要提供的特殊标记。密钥通常作为请求标头发送：

```TEXT
GET /something HTTP/1.1
X-API-Key: abcdef12345
```

或者作为查询参数：

```TEXT
GET /something?api_key=abcdef12345
```

API密钥应该是只有客户端和服务器知道的秘密。但是，除了基本身份验证之外，基于API密钥的身份验证不被视为安全，除非与其他安全机制（如HTTPS / SSL）一起使用。  
  
要定义基于API密钥的安全性：

* `type: apiKey` 在全局`securityDefinitions`部分中添加条目。条目名称可以是任意的（例如下面示例中的APIKeyHeader），用于从规范的其他部分引用此安全性定义。
* 指定是否传递 API密钥`in: header`或`in: query`。
* 为该参数或标题指定`name` 。

```YAML
securityDefinitions:
   # X-API-Key: abcdef12345
   APIKeyHeader:
     type: apiKey
     in: header
     name: X-API-Key
   # /path?api_key=abcdef12345
   APIKeyQueryParam:
     type: apiKey
     in: query
     name: api_key
```

然后，使用`security`根级别或操作级别上的部分将 API密钥应用于整个 API或特定操作。

```YAML
# Global security (applies to all operations):
security:
  - APIKeyHeader: []
paths:
  /something:
    get:
      # Operation-specific security:
      security:
        - APIKeyQueryParam: []
      responses:
        200:
          description: OK (successfully authenticated)
```

请注意，可以在API中支持多种授权类型。请参阅使用[Using Multiple Authentication Types](Authentication.md/#using-multiple-quthentication-types)。

## Pair of API Keys

某些API使用一对安全密钥，例如 API 密钥和App ID。要指定密钥一起使用（如在逻辑AND中），请将它们列在数组中的相同数组项中`security`：

```YAML
securityDefinitions:
  apiKey:
    type: apiKey
    in: header
    name: X-API-KEY
  appId:
    type: apiKey
    in: header
    name: X-APP-ID
security:
  - apiKey: []
    appId: []
```

注意区别于：

```YAML
security:
  - apiKey: []
  - appId: []
```

这意味着可以使用任一键（如在逻辑OR中）。更多的例子，请参阅使用多个身份验证类型。

## 401 Response

您还可以为缺少或无效API密钥的请求定义401“未授权”响应。此响应包括`WWW-Authenticate`标题，您可能需要提及。与其他常见响应一样，401响应可以在全局`responses`部分中定义，并从多个操作中引用。

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
    description: API key is missing or invalid
    headers:
      WWW_Authenticate:
        type: string
```