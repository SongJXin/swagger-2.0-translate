# Describing Responses

API规范需要指定 responses 所有API操作。每个操作必须至少定义一个响应，通常是成功的响应。响应由其HTTP状态代码和响应正文和/或标头中返回的数据定义。这是一个最小的例子：

```yaml
paths:
  /ping:
    get:
      produces:
        - application/json
      responses:
        200:
          description: OK
```

## Response Media Types

API可以使用各种媒体类型进行响应。JSON是最常用的数据交换格式，但不是唯一可能的格式。要指定响应媒体类型，请produces在根级别或操作级别使用关键字。可以在操作级别覆盖全局列表。

```YAML
produces:
  - application/json
paths:
  # This operation returns JSON - as defined globally above
  /users:
    get:
      summary: Gets a list of users.
      responses:
        200:
          description: OK
  # Here, we override the "produces" array to specify other media types
  /logo:
    get:
      summary: Gets the logo image.
      produces:
        - image/png
        - image/gif
        - image/jpeg
      responses:
        200:
          description: OK
```

更过信息 [MIME类型](#MIME_Types.md)

## HTTP Status Codes

在下`responses`，每个响应定义以状态代码开头，例如200或404.操作通常返回一个成功的状态代码和一个或多个错误状态。每个响应状态都需要一个`description`。例如，您可以描述错误响应的条件。[GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/)可用于富文本表示。

```YAML
      responses:
        200:
          description: OK
        400:
          description: Bad request. User ID must be an integer and bigger than 0.
        401:
          description: Authorization information is missing or invalid.
        404:
          description: A user with the specified ID was not found.
```

请注意，API规范不一定需要涵盖所有可能的 HTTP响应代码，因为它们可能事先不知道。但是，预计它将涵盖成功的响应和任何已知错误。“已知错误”是指，例如，对于通过ID返回资源的操作的404 Not Found响应，或者对于无效操作参数的400 Bad Request响应。

## Response Body

该schema关键字用于描述响应主体。架构可以定义：

* object 或array- 通常与 JSON 和XML API一起使用，
* 诸如数字或字符串之类的原语 - 用于纯文本响应，
* file（见[下文](#response-that-returns-a-file)）。

可以在操作中内联定义模式：

```YAML
      responses:
        200:
          description: A User object
          schema:
            type: object
            properties:
              id:
                type: integer
                description: The user ID.
              username:
                type: string
                description: The user name.
```

或在根级别定义并通过引用`$ref`。如果多个响应使用相同的模式，这将非常有用。

```YAML
      responses:
        200:
          description: A User object
          schema:
            $ref: '#/definitions/User'
definitions:
  User:
    type: object
    properties:
      id:
        type: integer
        description: The user ID.
      username:
        type: string
        description: The user name.
```

## Response That Returns a File

API操作可以返回文件，比如图像或PDF。在这种情况下，请定义响应`schema`，`type: file` 并在该 `produces` 部分中指定相应的 MIME 类型。

```YAML
paths:
  /report:
    get:
      summary: Returns the report in the PDF format.
      produces:
        - application/pdf
      responses:
        200:
          description: A PDF file.
          schema:
            type: file
```

## Empty Response Body

一些回复，例如204 No Content，没有正文。要指示响应主体为空，请不要为响应指定`schema`。Swagger没有`schema`视为没有正文的响应。

```YAML
      responses:
        204:
          description: The resource was deleted successfully.
```

## Response Headers

来自API的响应可以包括自定义标头，以提供有关API调用结果的其他信息。例如，速率受限的API可以通过响应头提供速率限制状态，如下所示：

```TEXT
HTTP 1/1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 2016-10-12T11:00:00Z
{ ... }
```

您可以`headers`为每个响应定义自定义，如下所示：

```YAML
paths:
  /ping:
    get:
      summary: Checks if the server is alive.
      responses:
        200:
          description: OK
          headers:
            X-RateLimit-Limit:
              type: integer
              description: Request limit per hour.
            X-RateLimit-Remaining:
              type: integer
              description: The number of requests left for the time window.
            X-RateLimit-Reset:
              type: string
              format: date-time
              description: The UTC date/time at which the current rate limit window resets.
```

请注意，目前，Swagger 无法为不同的响应代码或不同的 API 操作定义公共响应头。您需要单独为每个响应定义标头。

## Default Response

有时，操作可以使用不同的HTTP状态代码返回多个错误，但所有错误都具有相同的响应结构：

```YAML
      responses:
        200:
          description: Success
          schema:
            $ref: '#/definitions/User'
        400:
          description: Bad request
          schema:
            $ref: '#/definitions/Error'    # <-----
        404:
          description: Not found
          schema:
            $ref: '#/definitions/Error'    # <-----
```

您可以使用default响应来共同描述这些错误，而不是单独描述。“默认”表示此响应用于此操作未单独涵盖的所有HTTP代码。

```YAML
      responses:
        200:
          description: Success
          schema:
            $ref: '#/definitions/User'
        # Definition of all error statuses
        default:
          description: Unexpected error
          schema:
            $ref: '#/definitions/Error'
```

## Reusing Responses

如果多个操作返回相同的响应（状态代码和数据），则可以在全局responses部分中定义它并通过$ref操作级别引用该定义。这对于具有相同状态代码和响应正文的错误响应非常有用。

```YAML
paths:
  /users:
    get:
      summary: Gets a list of users.
      response:
        200:
          description: OK
          schema:
            $ref: '#/definitions/ArrayOfUsers'
        401:
          $ref: '#/responses/Unauthorized'   # <-----
  /users/{id}:
    get:
      summary: Gets a user by ID.
      response:
        200:
          description: OK
          schema:
            $ref: '#/definitions/User'
        401:
          $ref: '#/responses/Unauthorized'   # <-----
        404:
          $ref: '#/responses/NotFound'       # <-----
# Descriptions of common responses
responses:
  NotFound:
    description: The specified resource was not found
    schema:
      $ref: '#/definitions/Error'
  Unauthorized:
    description: Unauthorized
    schema:
      $ref: '#/definitions/Error'
definitions:
  # Schema for error response body
  Error:
    type: object
    properties:
      code:
        type: string
      message:
        type: string
    required:
      - code
      - message
```

请注意，在根级别定义的响应不会自动应用于所有操作。这些只是可以由多个操作引用和重用的定义。

## A&Q

### 我可以根据请求参数获得不同的响应吗？如

```TEXT
GET /something -> {200, schema_1}
GET /something?foo=bar -> {200, schema_2}
```

不，这不受支持。