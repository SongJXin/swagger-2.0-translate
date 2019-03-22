# Adding Examples

API规范可以包含以下示例：

* 响应MIME类型，
* 模式（数据模型），
* 模式中的各个属性。

示例可以由工具和库使用，例如，Swagger UI基于输入模式示例自动填充请求主体，并且一些API模拟工具使用示例来生成模拟响应。  
  
**注意**：不要将示例值与`default`值混淆。一个例子用于说明该值应该是什么样的。如果请求中未提供值，则默认值是服务器使用的值。

## Schema Examples

该`example`密钥被用来提供一个模式例子。可以为各个属性，对象和整个模式提供示例。

### Property Examples

属性示例可以内联指定。示例值必须符合属性类型。

```YAML
definitions:
  CatalogItem:
    type: object
    properties:
      id:
        type: integer
        example: 38
      title:
        type: string
        example: T-shirt
    required:
      - id
      - title
```

请注意，不支持每个属性或架构的多个示例值，也就是说，您不能拥有：

```YAML
title:
  type: string
  example: T-shirt
  example: Phone
```

### Object Examples

类型对象的属性可以包含复杂的内联示例，其中包含该对象的属性。该示例应符合对象架构。

```YAML
definitions:
  CatalogItem:
    type: object
    properties:
      id:
        type: integer
        example: 38
      title:
        type: string
        example: T-shirt
      image:
        type: object
        properties:
          url:
            type: string
          width:
            type: integer
          height:
            type: integer
        required:
          - url
        example:   # <-----
          url: images/38.png
          width: 100
          height: 100
    required:
      - id
      - title
```

### Array Examples

基元数组的示例：

```YAML
definitions:
  ArrayOfStrings:
    type: array
    items:
      type: string
    example:
      - foo
      - bar
      - baz
```

同样，对象数组将指定为：

```YAML
definitions:
  ArrayOfCatalogItems:
    type: array
    items:
      $ref: '#/definitions/CatalogItem'
    example:
      - id: 38
        title: T-shirt
      - id: 114
        title: Phone
```

### Whole Schema Examples

`example`可以为整个模式（包括所有嵌套模式）指定一个，前提是该示例符合模式。

```YAML
definition:
  CatalogItem:
    type: object
    properties:
      id:
        type: integer
      name:
        type: string
      image:
        type: object
        properties:
          url:
            type: string
          width:
            type: integer
          height:
            type: integer
    required:
      - id
      - name
    example:   # <----------
      id: 38
      name: T-shirt
      image:
        url: images/38.png
        width: 100
        height: 100
```

### Response Examples

Swagger允许响应级别上的示例，每个示例对应于操作返回的特定MIME类型。例如一个例子`application/json`，另一个例子`text/csv`等等。每种 MIME 类型必须是操作的`produces`值之一 - 显式或从全局范围继承。

```YAML
      produces:
        - application/json
        - text/csv
      responses:
        200:
          description: OK
          examples:
            application/json: { "id": 38, "title": "T-shirt" }
            text/csv: >
              id,title
              38,T-shirt
```

所有示例都是自由格式的，这意味着它们的解释取决于工具和库。

### JSON and YAML Examples

由于 JSON和 YAML是可互换的（YAML是 JSON的超集），因此可以使用JSON语法指定两者：

```YAML
          examples:
            application/json:
              {
                "id": 38,
                "title": "T-shirt"
              }
```

或YAML语法：

```YAML
          examples:
            application/json:
              id: 38
              title: T-shirt
              image:
                url: images/38.png
```

### XML Examples

XML响应示例没有特定的语法。但是，由于响应示例是自由格式的，因此您可以使用您希望或工具支持的任何格式。

```YAML
          examples:
            application/xml: '<users><user>Alice</user><user>Bob</user></users>'
          examples:
            application/xml:
              users:
                user:
                  - Alice
                  - Bob
          examples:
            application/xml:
              url: http://myapi.com/examples/users.xml
```

### Text Examples

由于所有响应示例都是自由格式，因此您可以使用工具或库支持的任何格式。例如，类似于：

```YAML
          examples:
            text/html: '<html><body><p>Hello, world!</p></body></html>'
            text/plain: Hello, world!
```

有关如何在YAML中编写多行字符串的提示，另请参阅[Stack Overflow上的这篇文章](https://stackoverflow.com/questions/3790454/in-yaml-how-do-i-break-a-string-over-multiple-lines)。

### Example Precedence

如果在不同级别（属性，架构，响应）上有多个示例，则处理规范的工具将使用更高级别的示例。也就是说，优先顺序是：

1. 响应示例
2. 架构示例
3. 对象和数组属性示例
4. 原子属性示例和数组项示例

### Examples and $ref

OpenAPI 2.0 `example`和`examples`关键字需要内联示例，**不支持**`$ref`。示例值按原样显示，因此`$ref`将显示为名为的对象属性`$ref`。