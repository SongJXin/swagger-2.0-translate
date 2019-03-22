# MIME Types

API可以接受和返回不同格式的数据，最常见的格式是 JSON 和 XML 。您可以使用`consumes`和`produces`关键字来指定 API 所理解的 MIME 类型。`consumes`和`produces`的值是 MIME 类型数组。全局 MIME 类型可以在 API 规范的根级别上定义，并由所有API操作继承。这里的 API 使用 JSON 和 XML :

```YAML
consumes:
  - application/json
  - application/xml
produces:
  - application/json
  - application/xml
```

注意，`consumes` 只影响包含请求体的操作，比如POST、PUT 和 PATCH。对于像GET这样的无实体操作，则忽略它。在操作级别上使用时，`consumes`和`produces`会覆盖(而不是扩展)全局定义。在下面的例子中，`GET /logo`操作重新定义了`produces`数组来返回图像:

```YAML
paths:
  /logo:
    get:
      summary: Returns the logo image
      produces:
        - image/png
        - image/gif
        - image/jpeg
      responses:
        200:
          description: OK
          schema:
            type: file
```

在`consumes`和`produces`中列出的MIME类型应该符合 [RFC 6838](http://tools.ietf.org/html/rfc6838)。例如，你可以使用标准的MIME类型，例如:

```YAML
application/json
application/xml
application/x-www-form-urlencoded
multipart/form-data
text/plain; charset=utf-8
text/html
application/pdf
image/png
```

以及特定于供应商的MIME类型(由vnd表示):

```YAML
application/vnd.mycompany.myapp.v2+json
application/vnd.ms-excel
application/vnd.openstreetmap.data+xml
application/vnd.github-issue.text+json
application/vnd.github.v3.diff
image/vnd.djvu
```
