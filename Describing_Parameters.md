# Describing Parameters

在Swagger中，API 操作参数是在操作定义中的 parameters 部分中定义的。每个参数都有`name`、值`type`(用于基本值参数)或`schema`(用于请求主体)和可选`description`。举个例子:

```YAML
paths:
  /users/{userId}:
    get:
      summary: Gets a user by ID.
      parameters:
        - in: path
          name: userId
          type: integer
          required: true
          description: Numeric ID of the user to get.
```

注意，`parameters`是一个数组，因此，在YAML中，每个参数定义必须在其前面加上破折号(`-`)。

## 参数类型

Swagger根据参数位置区分以下参数类型。位置由参数的`in`键决定，例如`in: query` 或`in: path`。

* [query parameters](#query-parameters), 例如 `/users?role=admin`
* [path parameters](#path-parameters), 例如`/users/{id}`
* [header parameters](#header-parameters), 例如`X-MyHeader: Value`
* 描述POST、PUT 和 PATCH 请求主体的主体参数([Describing Request Body](Describing_Request_Body.md))
* [form parameters](#form-parameters)用于描述具有`Content-Type`为`application/x-www-form-urlencoded`和`multipart/form-data`的请求的有效负载的各种主体参数(后者通常用于[文件上传](File_Upload.md));

### Query Parameters

查询参数是最常见的参数类型。它们出现在请求URL末尾的问号(`?`)后面，不同的`name=value`对由`&`分隔。查询参数可以是必需的，也可以是可选的。

```YAML
GET /pets/findByStatus?status=available
GET /notes?offset=100&limit=50
```

使用`in: query`表示查询参数:

```YAML
     parameters:
        - in: query
          name: offset
          type: integer
          description: The number of items to skip before starting to collect the result set.
        - in: query
          name: limit
          type: integer
          description: The numbers of items to return.
```

查询参数只支持基本类型。可以有一个`array`，但`items`必须是基本值类型。对象不受支持。  
**注意**:要描述作为查询参数传递的API密钥，请使用安全定义。参考[API密钥](API_Keys.md)。

### Path Parameters

`Path Parameters`是可变URL路径的一部分。它们通常用于指向集合中的特定资源，例如由ID标识的用户。URL可以有几个路径参数，每个参数用大括号{}表示。

```YAML
GET /users/{id}
GET /cars/{carId}/drivers/{driverId}
```

当客户机调用API时，必须用实际值替换每个path参数。在Swagger中，根据需要使用`In: path`和其他属性定义路径参数。参数名称必须与路径中指定的相同。另外，请记住添加`required: true`，因为路径参数总是必需的。下面是`GET /users/{id}`的例子:

```YAML
paths:
  /users/{id}:
    get:
      parameters:
        - in: path
          name: id   # Note the name is the same as in the path
          required: true
          type: integer
          minimum: 1
          description: The user ID.
       responses:
         200:
           description: OK
```

路径参数可以是多值的，比如`GET /users/12,34,56`。这是通过将参数类型指定为`array`来实现的。参考[Array and Multi-Value Parameters](Describing_Parameters.md/#array-and-multi-value-parameters)

### Header Parameters

API 调用可能需要使用 HTTP 请求发送自定义头。Swagger允许您通过`in: header`参数定义自定义请求头。例如，假设一个`GET /ping`调用需要`X-Request-ID`头:

```YAML
GET /ping HTTP/1.1
Host: example.com
X-Request-ID: 77e1c83b-7bb0-437b-bc50-a7a58e5660ac
```

在Swagger中，你可以这样定义这个操作:

```YAML
paths:
  /ping:
    get:
      summary: Checks if the server is alive.
      parameters:
        - in: header
          name: X-Request-ID
          type: string
          required: true
```

以类似的方式，您可以定义[自定义响应头](Describing_Responses.md/#headers)。  
**注意**:Swagger规范对一些头文件有特殊的关键字:

Header|Swagger Keywords|更多信息，请参考
---|---|---
`Content-Type`|`consumes`(request content type)`produces`(response content type)|[MIME Types](#MIME_Types.md)
`Accept`|`produces`|[MIME Types](#MIME_Types.md)
`Authorization`|`securityDefinitions, security`|[Authentication](Authentication.md)

### Form Parameters

表单参数用于描述`Content-Type`为:

* `application/x-www-form-urlencoded` (用于 POST 原始值和原始值数组)。
* `multipart/form-data` (用于上传文件或文件与原始数据的组合)。

也就是说，操作的`consumes`必须指定这些内容类型之一。：表单参数定义如`in: formData`,它们只能是基元(字符串、数字、布尔值)或基元数组(这意味着不能使用`$ref`作为`items`值)。此外，表单参数不能与`in: body`参数共存，因为`formData`是描述主体的一种特定方式。要说明表单参数，请考虑HTML POST表单:

```HTML
<form action="http://example.com/survey" method="post">
  <input type="text"   name="name" />
  <input type="number" name="fav_number" />
  <input type="submit"/>
 </form>
```

此表单将数据发布到表单的端点:

```TEXT
POST /survey HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 29
name=Amy+Smith&fav_number=321
```

在Swagger中，可以将端点描述为:

```YAML
paths:
  /survey:
    post:
      summary: A sample survey.
      consumes:
        - application/x-www-form-urlencoded
      parameters:
        - in: formData
          name: name
          type: string
          description: A person's name.
        - in: formData
          name: fav_number
          type: number
          description: A person's favorite number.
      responses:
        200:
          description: OK
```

要了解如何为文件上载定义表单参数，请参见[文件上载](File_Upload.md)。

### Required and Optional

默认情况下，Swagger将所有请求参数都视为可选的。您可以添加`required: true`来将参数标记为`required`。注意，路径参数必须是`required: true`，因为它们总是必需的。

```YAML
      parameters:
        - in: path
          name: userId
          type: integer
          required: true    # <----------
          description: Numeric ID of the user to get.
```

### Default Parameter Values

可以使用`default`字段作为可选参数指定默认值。如果客户机没有在请求中提供参数值，则服务器使用默认值。值类型必须与参数的数据类型相同。一个典型的例子是分页参数，如偏移量和限制:

```TEXT
GET /users
GET /users?offset=30&limit=10
```

假设偏移默认值为0，并将默认值限制为20，范围为0到100，您可以将这些参数定义为:

```YAML
      parameters:
        - in: query
          name: offset
          type: integer
          required: false
          default: 0
          minimum: 0
          description: The number of items to skip before starting to collect the result set.
        - in: query
          name: limit
          type: integer
          required: false
          default: 20
          minimum: 1
          maximum: 100
          description: The numbers of items to return.
```

#### Common Mistakes

使用default关键字有两个常见错误:

* 对`required`参数或属性使用`default`，例如，对路径参数。这是没有意义的——如果一个值是`required`，客户端必须始终发送它，`default`值永远不会用到。
* 使用`default`指定示例值。这不是默认的用途，可能会导致一些 Swagger 工具出现意想不到的行为。可以使用`example`或`examples`达到此目的

### Enum Parameters

`enum`关键字允许您将参数值限制为一组固定的值。枚举值必须与参数`type`相同。

```YAML
        - in: query
          name: status
          type: string
          enum: [available, pending, sold]
```

更多信息：[Defining an Enum](Enums.md)

### Array and Multi-Value Parameters

路径、查询、标头和表单参数可以接受一个值列表，例如:

```TEXT
GET /users/12,34,56,78
GET /resource?param=value1,value2,value3
GET /resource?param=value1&param=value2&param=value3
POST /resource
param=value1&param=value2
```

`multi-value parameter`必须通过`type: array`和适当的`collectionFormat`定义。

```YAML
      # color=red,black,white
      parameters:
        - in: query
          name: color
          type: array
          collectionFormat: csv
          items:
            type: string
```

`collectionFormat`指定数组格式(具有多个参数或具有相同名称的多个参数的单个参数)和数组项的分隔符。

collectionFormat|描述|例子
---|---|---
`csv` (默认)|逗号分隔的值。|`foo,bar,baz`
`ssv`|空格分隔的值|`foo bar baz`
`tsv`|制表符分隔的值|`"foo\tbar\tbaz"`
`pipes`|管道分隔值。|`foo|bar|baz`
`multi`|多个参数实例而不是多个值。仅支持`in: query`和`in: formData`参数。|`foo=value&foo=another_value`

此外，您可以：

* 使用`minItems`和`maxItems`控制数组的大小，
* 执行`uniqueItems`，
* 将数组项限制为一组固定的`enum`值。

例如：

```YAML
        - in: query
          name: color
          required: false
          type: array
          minItems: 1
          maxItems: 5
          uniqueItems: true
          items:
            type: string
            enum: [black, white, gray, red, pink, orange, yellow, green, blue, purple, brown]
```

如果省略此参数，还可以指定服务器将使用的默认数组：

```YAML
        - in: query
          name: sort
          required: false
          type: array
          items:
            type: string
          default: ["-modified", "+id"]
```

### Constant Parameters

您可以将常量参数定义为只有一个可能值的必需参数：

```YAML
- required: true
  enum: [value]
```

该`enum`属性指定可能的值。在此示例中，只能使用一个值，这将是Swagger UI中可供用户选择的唯一值。  
**注意**：常量参数与[默认参数值](#default-parameter-values)不同。常量参数始终由客户端发送，而默认值是服务器在客户端未发送参数时使用的值。

### Parameters Without a Value

查询字符串和表单数据参数可能只有名称而没有值：

```TEXT
GET /foo?metadata
POST /something
foo&bar&baz
```

使用`allowEmptyValue`来形容这样的参数：

```YAML
        - in: query
          name: metadata
          required: true
          type: boolean
          allowEmptyValue: true  # <-----
```

### Common Parameters

#### 一个路径的所有方法的公共参数

可以在路径本身下定义参数，在这种情况下，参数存在于此路径下描述的所有操作中。一个典型的例子是GET / PUT / PATCH / DELETE操作，它们操作通过path参数访问的相同资源。

```YAML
paths:
  /user/{id}:
    parameters:
      - in: path
        name: id
        type: integer
        required: true
        description: The user ID.
    get:
      summary: Gets a user by ID.
      ...
    patch:
      summary: Updates an existing user with the specified ID.
      ...
    delete:
      summary: Deletes the user with the specified ID.
      ...
```

在操作级别定义的任何额外参数与路径级参数一起使用：

```YAML
paths:
  /users/{id}:
    parameters:
      - in: path
        name: id
        type: integer
        required: true
        description: The user ID.
    # GET/users/{id}?metadata=true
    get:
      summary: Gets a user by ID.
      # Note we only define the query parameter, because the {id} is defined at the path level.
      parameters:
        - in: query
          name: metadata
          type: boolean
          required: false
          description: If true, the endpoint returns only the user metadata.
      responses:
        200:
          description: OK
```

可以在操作级别覆盖特定的路径级参数，但不能删除。

```YAML
paths:
  /users/{id}:
    parameters:
      - in: path
        name: id
        type: integer
        required: true
        description: The user ID.
    # DELETE /users/{id} - uses a single ID.
    # Reuses the {id} parameter definition from the path level.
    delete:
      summary: Deletes the user with the specified ID.
      responses:
        204:
          description: User was deleted.
    # GET /users/id1,id2,id3 - uses one or more user IDs.
    # Overrides the path-level {id} parameter.
    get:
      summary: Gets one or more users by ID.
      parameters:
        - in: path
          name: id
          required: true
          description: A comma-separated list of user IDs.
          type: array
          items:
            type: integer
          collectionFormat: csv
          minItems: 1
      responses:
        200:
          description: OK
```

#### Common Parameters in Different Paths

不同的API路径可以具有一些共同的参数，例如分页参数。您可以在全局`parameters`部分中定义公共参数，并通过单独的操作引用它们`$ref`。

```YAML
parameters:
  offsetParam:  # <-- Arbitrary name for the definition that will be used to refer to it.
                # Not necessarily the same as the parameter name.
    in: query
    name: offset
    required: false
    type: integer
    minimum: 0
    description: The number of items to skip before starting to collect the result set.
  limitParam:
    in: query
    name: limit
    required: false
    type: integer
    minimum: 1
    maximum: 50
    default: 20
    description: The numbers of items to return.
paths:
  /users:
    get:
      summary: Gets a list of users.
      parameters:
        - $ref: '#/parameters/offsetParam'
        - $ref: '#/parameters/limitParam'
      responses:
        200:
          description: OK
  /teams:
    get:
      summary: Gets a list of teams.
      parameters:
        - $ref: '#/parameters/offsetParam'
        - $ref: '#/parameters/limitParam'
      responses:
        200:
          description: OK
```

请注意，全局`parameters`不是应用于所有操作的参数 - 它们只是可以轻松重用的全局定义。

### Parameter Dependencies

Swagger不支持参数依赖性和互斥参数。<https://github.com/OAI/OpenAPI-Specification/issues/256> 上有一个开放功能请求。您可以做的是记录参数描述中的限制，并在400 Bad Request响应中定义逻辑。例如，考虑`/report`接受相对日期范围（`rdate`）或精确范围（`start_date+ end_date`）的端点：

```TEXT
GET /report?rdate=Today
GET /report?start_date=2016-11-15&end_date=2016-11-20
```

您可以按如下方式描述此端点：

```YAML
paths:
  /report:
    get:
      parameters:
        - name: rdate
          in: query
          type: string
          description: >
             A relative date range for the report, such as `Today` or `LastWeek`.
             For an exact range, use `start_date` and `end_date` instead.
        - name: start_date
          in: query
          type: string
          format: date
          description: >
            The start date for the report. Must be used together with `end_date`.
            This parameter is incompatible with `rdate`.
        - name: end_date
          in: query
          type: string
          format: date
          description: >
            The end date for the report. Must be used together with `start_date`.
            This parameter is incompatible with `rdate`.
      responses:
        400:
          description: Either `rdate` or `start_date`+`end_date` are required.
```

## FAQ

### 我什么时候应该使用“type”vs“schema”

`schema`仅用于`in: body`参数。任何其他期望的参数基本类型，如`type: string`，或`array`原语。

### 我可以将对象作为查询参数吗

这在OpenAPI 3.0中是可能的，但在2.0中则不行。