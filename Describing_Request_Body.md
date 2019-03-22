# Describing Request Body

POST，PUT 和 PATCH 请求可以具有请求主体（有效负载），例如 JSON 或 XML 数据。在Swagger术语中，请求主体称为主体参数。虽然操作可能有其他参数（路径，查询，标题），但只能有一个body参数。  
**注意**：使用[表单参数](https://swagger.io/docs/specification/2-0/describing-parameters/#form-parameters)而不是正文参数来描述`application/x-www-form-urlencoded`和`multipart/form-data`请求的有效负载。

body参数在操作的参数部分中定义，包括以下内容：

* `in: body`
* `schema`描述了主体数据类型和结构。数据类型通常是对象，但也可以是基元（例如字符串或数字）或数组。
* 可选`description`。
* 有效负载名称。它是必需的但被忽略（仅用于文档目的）。

## Object Payload (JSON, etc.)

许多API将数据作为对象传输，例如JSON。`schema`对象应该为该对象指定`type: object`和属性。例如，`POST /users`使用此JSON正文的操作：

```JSON
{
  "userName":  "Trillian",
  "firstName": "Tricia",
  "lastName":  "McMillan"
}
```

可以描述为：

```YAML
paths:
  /users:
    post:
      summary: Creates a new user.
      consumes:
        - application/json
      parameters:
        - in: body
          name: user
          description: The user to create.
          schema:
            type: object
            required:
              - userName
            properties:
              userName:
                type: string
              firstName:
                type: string
              lastName:
                type: string
      responses:
        201:
          description: Created
```

**注意**：忽略body参数的名称。它仅用于文档目的。对于更模块化的样式，您可以将架构定义移动到全局`definitions`部分，并使用`$ref`以下内容引用它们：

```YAML
paths:
  /users:
    post:
      summary: Creates a new user.
      consumes:
        - application/json
      parameters:
        - in: body
          name: user
          description: The user to create.
          schema:
            $ref: '#/definitions/User'     # <----------
     responses:
         200:
           description: OK
definitions:
  User:           # <----------
    type: object
    required:
      - userName
    properties:
      userName:
        type: string
      firstName:
        type: string
      lastName:
        type: string
```

## Primitive Body

想要POST / PUT只有一个值吗？没问题，您可以将主体模式类型定义为基元，例如字符串或数字。原始要求：

```TEXT
POST /status HTTP/1.1
Host: api.example.com
Content-Type: text/plain
Content-Length: 42
Time is an illusion. Lunchtime doubly so.
```

Swagger定义：

```YAML
paths:
  /status:
    post:
      summary: Updates the status message.
      consumes:
        - text/plain      # <----------
    parameters:
      - in: body
        name: status
        required: true
        schema:
          type: string  # <----------
    responses:
      200:
        description: Success!
```