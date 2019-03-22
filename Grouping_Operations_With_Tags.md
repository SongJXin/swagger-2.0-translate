# Grouping Operations With Tags

您可以`tags`为每个API操作分配一个列表。工具和库可以不同地处理标记操作。例如，Swagger UI用于`tags`对显示的操作进行分组。

```YAML
paths:
  /pet/findByStatus:
    get:
      summary: Finds pets by Status
      tags:
        - pets
      ...
  /pet:
    post:
      summary: Adds a new pet to the store
      tags:
        - pets
      ...
  /store/inventory:
    get:
      summary: Returns pet inventories
      tags:
        - store
      ...
```

Swagger UI中的标签或者，您可以指定`description`并`externalDocs`通过使用全球每个标签`tags`上的根级别部分。此处的标记名称应与操作中使用的名称相匹配。

```YAML
tags:
  - name: pets
    description: Everything about your Pets
    externalDocs:
      url: http://docs.my-api.com/pet-operations.htm
  - name: store
    description: Access to Petstore orders
    externalDocs:
      url: http://docs.my-api.com/store-orders.htm
```

全局标签部分中的标签顺序还控制Swagger UI中的默认排序。请注意，即使未在根级别定义标记，也可以在操作中使用标记。
![ ](https://swagger.io/swagger/media/Images/swagger-ui-tags-(1).png)