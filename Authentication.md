# Authentication

Swagger 2.0允许您为API定义以下身份验证类型：

* [基本认证](Basic_Authentication.md)
* [API密钥](API_Keys.md)（作为标头或查询字符串参数）
* OAuth 2常见流程（授权代码，隐式，资源所有者密码凭据，客户端凭据）

请按照上面的链接获取特定于这些身份验证类型的示例，或者继续阅读以了解如何一般性地描述身份验证。  

使用`securityDefinitions` 和`security`关键字描述身份验证。您可以使用它`securityDefinitions`来定义API支持的所有身份验证类型，然后使用`security`将特定身份验证类型应用于整个API或单个操作。  

该`securityDefinitions`部分用于定义API支持的所有安全方案（身份验证类型）。它是一个名称 - >定义映射，它将任意名称映射到安全方案定义。这里，API支持三种名为*BasicAuth*，*ApiKeyAuth*和*OAuth2*的安全方案，这些名称将用于从其他地方引用这些安全方案：

```YAML
securityDefinitions:
  BasicAuth:
    type: basic
  ApiKeyAuth:
    type: apiKey
    in: header
    name: X-API-Key
  OAuth2:
    type: oauth2
    flow: accessCode
    authorizationUrl: https://example.com/oauth/authorize
    tokenUrl: https://example.com/oauth/token
    scopes:
      read: Grants read access
      write: Grants write access
      admin: Grants read and write access to administrative information
```

每个安全方案可以是type：

* basic 用于基本身份验证
* apiKey 用于API密钥
* oauth2 对于OAuth 2

其他必需属性取决于安全类型。有关详细信息，请查看[Swagger规范](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securitySchemeObject)或我们的[基本身份验证](Basic_Authentication.md)和[API密钥示例](API_Keys.md)。  
在定义了安全方案后`securityDefinitions`，可以通过`security`分别在根级别或操作级别添加部分将它们应用于整个API或单个操作。在根级别上使用时，`security`将全局指定的安全方案应用于所有API操作，除非在操作级别上覆盖。  
在以下示例中，可以使用API​​密钥或OAuth 2对API调用进行身份验证.*ApiKeyAuth*和*OAuth2*名称是指之前定义的安全方案`securityDefinitions`。

```YAML
security:
  - ApiKeyAuth: []
  - OAuth2: [read, write]
```

`security`可以在单个操作中覆盖 Global ，以使用不同的身份验证类型，不同的OAuth 2范围或根本不进行身份验证：

```YAML
 paths:
  /billing_info:
    get:
      summary: Gets the account billing info
      security:
        - OAuth2: [admin]   # Use OAuth with a different scope
      responses:
        200:
          description: OK
        401:
          description: Not authenticated
        403:
          description: Access token does not have the required scope
  /ping:
    get:
      summary: Checks if the server is running
      security: []   # No security
      responses:
        200:
          description: Server is up and running
        default:
          description: Something is wrong
```

## Using Multiple Authentication Types

某些REST API支持多种身份验证类型。该`security`部分允许您使用逻辑 OR 和 AND 组合安全性要求，以实现所需的结果。`security`使用以下逻辑：

```YAML
security:    # A OR B
  - A
  - B
```

```YAML
security:    # A AND B
  - A
    B
```

```YAML
security:    # (A AND B) OR (C AND D)
  - A
    B
  - C
    D
```

也就是说，`security`是一个哈希映射数组，其中每个哈希映射包含一个或多个命名的安全方案。使用逻辑AND组合散列映射中的项目，并使用逻辑OR组合数组项目。通过OR组合的安全方案是替代方案 - 任何一个都可以在给定的上下文中使用。通过AND组合的安全方案必须在同一请求中同时使用。在这里，我们可以使用基本身份验证或API密钥：

```YAML
security:
  - basicAuth: []
  - apiKey: []
```

这里，API 要求在请求中包含一对API密钥：

```YAML
security:
  - apiKey1: []
    apiKey2: []
```

在这里，我们可以使用OAuth 2或一对API密钥：

```YAML
security:
  - oauth2: [scope1, scope2]
  - apiKey1: []
    apiKey2: []
```

## A&Q

### []在“securitySchemeName：[]”中的含义是什么？

[]是空数组的YAML / JSON语法。Swagger规范要求`security`数组中的项指定所需范围的列表，如下所示：

```YAML
security:
  - securityA: [scopeA1, scopeA2]
  - securityB: [scopeB1, scopeB2]
```

范围仅用于OAuth 2，因此Basic 和API关键`security`项使用空数组。

```YAML
security:
  - oauth2: [scope1, scope2]
  - BasicAuth: []
  - ApiKeyAuth: []
```