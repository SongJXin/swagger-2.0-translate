# Swagger Extensions

扩展或供应商扩展是自定义属性`x-`，例如`x-logo`。它们可用于描述标准Swagger规范未涵盖的额外功能。许多支持 Swagger的 API相关产品都使用扩展来记录自己的属性，例如Amazon API Gateway，ReDoc，APIMatic等。API规范的根级别以及以下位置支持扩展：

* `info` 部分
* `paths` 部分，个人路径和操作
* 操作参数
* `responses`
* `tags`
* 安全计划

扩展值可以是基元，数组，对象或`null`。如果值是对象或对象数组，则对象的属性名称不需要以对象开头`x-`。

## Example

使用Amazon API Gateway 自定义授权程序的API将包含与此类似的扩展：

```YAML
securityDefinitions:
  APIGatewayAuthorizer:
    type: apiKey
    name: Authorization
    in: header
    x-amazon-apigateway-authtype: oauth2
    x-amazon-apigateway-authorizer:
      type: token
      authorizerUri: arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:account-id:function:function-name/invocations
      authorizerCredentials: arn:aws:iam::account-id:role
      identityValidationExpression: "^x-[a-z]+"
      authorizerResultTtlInSeconds: 60
```