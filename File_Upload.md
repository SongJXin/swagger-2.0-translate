# File Upload

Swagger 2.0支持发送的文件上传`Content-Type: multipart/form-data`。也就是说，您的API服务器必须使用`multipart/form-data`此操作：

```YAML
consumes:
   - multipart/form-data
```

使用`formData`参数（非身体参数）定义操作有效负载。file参数必须具有`type: file`：

```YAML
paths:
   /upload:
     post:
       summary: Uploads a file.
       consumes:
         - multipart/form-data
       parameters:
         - in: formData
           name: upfile
           type: file
           description: The file to upload.
```

此定义对应于以下HTTP请求：

```TEXT
POST /upload
Host: example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Length: 204
------WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Disposition: form-data; name="upfile"; filename="example.txt"
Content-Type: text/plain
File contents go here.
------WebKitFormBoundaryqzByvokjOTfF9UwD--
```

Swagger UI使用文件输入控件显示文件参数，允许用户浏览要上载的本地文件。

![ ](https://swagger.io/swagger/media/Images/swagger-ui-file-upload.png)

## Upload a File + Other Data

文件参数可以与其他表单数据一起发送：

```YAML
      parameters:
        - in: formData
          name: upfile
          type: file
          required: true
          description: The file to upload.
        - in: formData
          name: note
          type: string
          required: false
          description: Description of file contents.
```

相应的HTTP请求有效负载将包含多个部分：

```TEXT
POST /upload
Host: example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Length: 332
------WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Disposition: form-data; name="upfile"; filename="example.txt"
Content-Type: text/plain
File contents go here.
------WebKitFormBoundarysKk4Z8KcYfU3u6Cs
Content-Disposition: form-data; name="note"
Uploading a file named "example.txt"
------WebKitFormBoundaryqzByvokjOTfF9UwD--
```

### Multiple Upload

您可以拥有多个命名文件参数，每个参数都单独定义：

```YAML
      parameters:
        - in: formData
          name: upfile1
          type: file
          required: true
        - in: formData
          name: upfile2
          type: file
          required: false
        - in: formData
          name: upfile3
          type: file
          required: false
```

但是，不支持上载任意数量的文件（文件数组）。<https://github.com/OAI/OpenAPI-Specification/issues/254>上有一个开放功能请求。目前，您可以使用二进制字符串数组作为上传任意数量文件的变通方法：

```YAML
type: array
items:
  type: string
  format: binary
```

请注意，这不会在Swagger UI中生成文件上载界面。

## FAQ

### 我可以通过PUT上传文件吗

Swagger 2.0支持文件上传请求`Content-Type: multipart/form-data`，但不关心HTTP方法。您可以使用POST，PUT或任何其他方法，前提是操作消耗`multipart/form-data`。Swagger 2.0不支持上载有效负载只是原始文件内容，因为它们不是`multipart/form-data`。也就是说，Swagger 2.0不支持以下内容：

```bash
curl --upload-file archive.zip http://example.com/upload
```

另请注意，Swagger UI 中的文件上载仅适用于POST请求，因为浏览器中的 HTML 表单仅支持 GET 和 POST 方法。

### 我可以为上传的文件定义内容类型吗

这在OpenAPI 3.0中受支持，但在OpenAPI / Swagger 2.0中不受支持。在2.0中，您可以说操作接受文件，但您不能说该文件属于特定类型或结构。

作为解决方法，可以使用[供应商扩展](https://swagger.io/docs/specification/swagger-extensions/)来扩展此功能，例如：

```YAML
- in: formData
  name: zipfile
  type: file
  description: Contents of the ZIP file.
  x-mimetype: application/zip
```