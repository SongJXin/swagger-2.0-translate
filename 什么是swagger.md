# 什么是swagger

翻译来自[swagger 2.0](https://swagger.io/docs/specification/2-0/what-is-swagger/)

Swagger 允许您描述 API 的结构，以便机器能够读取它们。Swagger 所有能力中最卓越的是 api 描述自身结构的能力。为什么他这么棒?通过阅读API的结构，我们可以自动构建漂亮的交互式 API 文档。我们还可以用多种语言为您的 API 自动生成服务端库，并探索其他可能性，比如自动化测试。Swagger 通过请求 API 返回包含整个 API 详细描述的 YAML 或 JSON 来实现这一点。这个文件本质上是您的API的资源列表，它遵循[OpenAPI规范](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)。该规范要求您包括以下信息:

* 您的API支持哪些操作?
* 您的API的参数是什么?它返回什么?
* 您的API需要一些授权吗?
* 甚至还有一些有趣的东西，比如术语、联系信息和使用API的许可。

您可以手动为您的 API 编写一个 Swagger 规范，或者让它从源代码中的注释自动生成。查看 [swagger.io/open-source-integrations](https://swagger.io/tools/open-source/open-source-integrations/)，让您从代码生成swagger。

## 我的 API 有一个 swagger 的说明。现在怎么办呢

Swagger 可以通过以下几种方式进一步推动您的API开发:

* 设计优先用户:使用[Swagger CodeGen](https://swagger.io/tools/swagger-codegen/)为您的API生成一个**服务器存根**。剩下的唯一事情就是实现服务器逻辑——您的API已经准备好投入使用了!
* 使用[Swagger CodeGen](https://swagger.io/tools/swagger-codegen/)为您的API生成超过40种语言的**客户端库**。
* 使用[Swagger UI](https://swagger.io/swagger-ui/)生成交互式API文档，让用户可以直接在浏览器中尝试API调用。
* 使用该规范将 API 相关工具连接到您的API。例如，将规范导入到[SoapUI](https://soapui.org/)，为您的API创建自动化测试。
* 查看与Swagger集成的[开源](https://swagger.io/open-source-integrations/)和[商业工具](https://swagger.io/commercial-tools/)。