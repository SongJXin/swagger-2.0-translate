# Paths and Operations

在Swagger术语中，**paths**是API公开的端点(资源)，例如/users或/reports/summary，**operations**是用于操作这些路径的HTTP方法，例如GET、POST 或 DELETE。

## Paths

API `paths`和`operations`在API规范的全局`paths`部分中定义。

```YAML
paths:
  /ping:
    ...
  /users:
    ...
  /users/{id}:
    ...
```

所有路径都相对于`basePath`(参见[API Host And Base URL](API_Host_and_Base_URL.md))。完整的请求URL构造为`scheme://host/basePath/path`。

### Path 模板

Swagger支持 Path 模板，这意味着您可以使用大括号{}将URL的部分标记为 [Path参数](Describing_Parameters.md/#path-parameters):

```YAML
/users/{id}
/organizations/{orgId}/members/{memberId}
/report.{format}
```

API 客户端在调用 API 时需要提供适当的参数值，例如`/users/5`或`/users/12`。

## Operations

对于每个路径，定义可用于访问该路径的操作(HTTP方法)。Swagger 2.0支持`get`、`post`、`put`、`patch`、`delete`、`head`和`options`。一个路径可以支持多个操作，例如，`GET /users`获取一个用户列表，`POST /users`添加一个新用户。Swagger 将唯一的操作定义为路径和 HTTP 方法的组合。这意味着同一路径不允许两个 GET 或两个 POST 方法——即使它们有不同的参数(参数对唯一性没有影响)。操作的最小示例:

```YAML
paths:
  /ping:
    get:
      responses:
        200:
          description: OK
```

参数和响应模式的更详细示例:

```YAML
paths:
  /users/{id}:
    get:
      summary: Gets a user by ID.
      description: >
        A detailed description of the operation.
        GitHub Flavored Markdown can be used for rich text representation,
        such as **bold**, *italic* and [links](https://swagger.io).
      operationId: getUsers
      tags:
        - users
      produces:
        - application/json
        - application/xml
      parameters:
        - name: id
          in: path
          description: User ID
          type: integer
          required: true
      responses:
        200:
          description: OK
          schema:
            $ref: '#/definitions/User'
      externalDocs:
        url: http://api.example.com/docs/user-operations/
        description: Learn more about User operations provided by this API.
definitions:
  User:
    type: object
    properties:
      id:
        type: integer
      name:
        type: string
    required:
      - id
      - name
```

操作支持用于文档目的的一些可选元素:

* 简短的`summary`和对操作的详细`description`。`description`可以是[多行](https://stackoverflow.com/questions/3790454/in-yaml-how-do-i-break-a-string-over-multiple-lines)，并支持[GitHub风格的标记](https://guides.github.com/features/mastering-markdown/)，用于丰富的文本表示。
* `tags`用于在Swagger UI中对操作进行分组。
* `externalDocs`允许引用包含其他文档的外部资源。

### Operations 参数

Swagger支持通过路径、查询字符串、头和请求体传递的操作参数。有关详细信息，请参见[描述参数](https://swagger.io/docs/specification/2-0/describing-parameters/#query-parameters)。

#### operationId

每个操作可以指定一个唯一的`operationId`。一些代码生成器使用这个值来命名代码中相应的方法。

```YAML
  /users:
     get:
       operationId: getUsers
       summary: Gets all users.
       ...
     post:
       operationId: addUser
       summary: Adds a new user.
       ...
   /user/{id}:
     get:
       operationId: getUserById
       summary: Gets a user by user ID.
       ...
```

#### Paths中的查询字符串

查询字符串参数不能包含在路径中。应该将它们定义为[查询参数](https://swagger.io/docs/specification/2-0/describing-parameters/#query-parameters)。不正确的:

```YAML
paths:
  /users?role={role}:
```

正确的：

```YAML
paths:
  /users:
    get:
      parameters:
        - in: query
          name: role
          type: string
          enum: [user, poweruser, admin]
          required: false
```

这也意味着不可能有多个仅在查询字符串中不同的路径，例如:

```YAML
GET /users?firstName=value&lastName=value
GET /users?role=value
```

这是因为 Swagger 将唯一的操作视为路径和HTTP方法的组合，而其他参数并不会使该操作唯一。相反，您应该使用独特的路径，如:

```YAML
GET /users/findByName?firstName=value&lastName=value
GET /users/findByRole?role=value
```

## 标记为过时

你可以把特定的操作标记为`deprecated`，表示它们应该被停用:

```YAML
  /pet/findByTags:
    get:
      deprecated: true
```

工具可以以特定的方式处理`deprecated`的操作。例如，Swagger UI以不同的风格显示它们:
![ ](https://swagger.io/swagger/media/Images/deprecated.png)