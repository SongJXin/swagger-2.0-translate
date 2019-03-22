# Enums

您可以使用enum关键字指定请求参数或模型属性的可能值。例如，sort参数：

```Text
GET /items?sort=[asc|desc]
```

可描述为

```YAML
paths:
  /items:
    get:
      parameters:
        - in: query
          name: sort
          description: Sort order
          type: string
          enum: [asc, desc]
```

在YAML中，您还可以为每行指定一个枚举值：

```YAML
          enum:
            - asc
            - desc
```

枚举中的所有值都必须符合指定的类型。如果需要指定枚举项的描述，可以description在参数或属性中执行此操作：

```YAML
          type: string
          enum:
            - asc
            - desc
          description: >
            Sort order:
             * asc - Ascending, from A to Z.
             * desc - Descending, from Z to A.
```

## Reusable Enums

OpenAPI 3.0 支持可重用的枚举定义。  

虽然Swagger 2.0没有对可重用枚举的内置支持，但可以使用 YAML锚点在 YAML中定义它们 - 前提是您的工具支持它们。锚是YAML的一个方便功能，您可以在其中标记键，`&anchor-name`然后进一步向下使用`*anchor-name`以引用该键的值。这使您可以轻松地跨YAML文件复制内容。  

**注意**：`&`必须在使用之前定义anchor（）。

```YAML
definitions:
  Colors:
    type: string
    enum: &COLORS
      - black
      - white
      - red
      - green
      - blue
    # OR:
    # enum: &COLORS [black, white, red, green, blue]
paths:
  /products:
    get:
      parameters:
        - in: query
          name: color
          required: true
          type: string
          enum: *COLORS
      responses:
        200:
          description: OK
```

如果您的工具的 YAML解析器支持 YAML合并键（<<），您可以使用此技巧来引用类型和枚举值。

```YAML
definitions:
  Colors: &COLORS
    type: string
    enum: [black, white, red, green, blue]
paths:
  /products:
    get:
      parameters:
        - in: query
          name: color
          required: true
          <<: *COLORS
      responses:
        200:
          description: OK
```