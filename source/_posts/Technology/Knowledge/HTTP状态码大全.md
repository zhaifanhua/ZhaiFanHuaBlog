---
title: HTTP状态码大全
date: 2022-06-12 01:18:26
updated: 2022-06-17 01:19:26
description: 常见 HttpStatusCode 含义查询
keywords: HTTP,状态码,HttpStatusCode
tags:
  - 基础知识
categories:
  - 基础知识
---

# HTTP状态码大全(常见 HttpStatusCode 含义查询)

------

HTTP状态码，即HTTP协议状态码，是我们访问网站时会遇到的，服务器端返回的Http响应码，不同的数字分别代表着不同的响应状态。我们在做SEO或做网页开发过程中需要了解5类比较重要的HTTP状态码，可以根据请求响应代码检查服务器及程序是否正常，判断网页处于什么工作状态。我们就需要了解不同的状态码分别是什么含义。

**下面我们列出常见五类HTTP状态码和它的详解说明：**


### 1、 HTTP Status Code 1xx 请求信息

这一组状态码表明这是一个临时性响应。此响应仅由状态行和可选的HTTP头组成，以一个空行结尾。由于HTTP／1.0未定义任何1xx状态码，所以不要向HTTP／1.0客户端发送1xx响应。

| Http状态码 | Http Status Code            | Http状态码含义中文说明 |
| :--------- | :-------------------------- | :--------------------- |
| **100**    | 100 Continue100            | 请继续请求             |
| **101**    | 101 Switching Protocols101 | 请切换协议             |
| **102**    | 102 Processing102          | 将继续执行请求         |

### 2、 HTTP Status Code 2xx 成功状态

这一组状态码表明客户端的请求已经被服务器端成功接收并正确解析。

| Http状态码 | Http Status Code                      | Http状态码含义中文说明                  |
| :--------- | :------------------------------------ | :-------------------------------------- |
| **200**    | 200 OK200                            | 请求成功                                |
| **201**    | 201 Created201                       | 请求已被接受，等待资源响应              |
| **202**    | 202 Accepted202                      | 请求已被接受，但尚未处理                |
| **203**    | 203 Non-Authoritative Information203 | 请求已成功处理，结果来自第三方拷贝      |
| **204**    | 204 No Content204                    | 请求已成功处理，但无返回内容            |
| **205**    | 205 Reset Content205                 | 请求已成功处理，但需重置内容            |
| **206**    | 206 Partial Content206               | 请求已成功处理，但仅返回了部分内容      |
| **207**    | 207 Multi-Status207                  | 请求已成功处理，返回了多个状态的XML消息 |
| **208**    | 208 Already Reported208              | 响应已发送                              |
| **226**    | 226 IM Used226                       | 已完成响应                              |

### 3、 HTTP Status Code 3xx 重定向状态

这一组状态码表示客户端需要采取更进一步的行动来完成请求。通常，这些状态码用来重定向，后续的请求地址（重定向目标）在本次响应的Location域中指明。

| Http状态码 | Http Status Code              | Http状态码含义中文说明         |
| :--------- | :---------------------------- | :----------------------------- |
| **300**    | 300 Multiple Choices300     | 返回多条重定向供选择           |
| **301**    | 301 Moved Permanently301    | 永久重定向                     |
| **302**    | 302 Found302                | 临时重定向                     |
| **303**    | 303 See Other303            | 当前请求的资源在其它地址       |
| **304**    | 304 Not Modified304         | 请求资源与本地缓存相同，未修改 |
| **305**    | 305 Use Proxy305            | 必须通过代理访问               |
| **306**    | 306 (已废弃Switch Proxy306 | (已废弃请切换代理)             |
| **307**    | 307 Temporary Redirect307   | 临时重定向，同302              |
| **308**    | 308 Permanent Redirect308   | 永久重定向，且禁止改变http方法 |

### 4、 HTTP Status Code 4xx 客户端错误

这一组状态码表示客户端的请求存在错误，导致服务器无法处理。除非响应的是一个HEAD请求，否则服务器就应该返回一个解释当前错误状况的实体，以及这是临时的还是永久性的状况。这些状态码适用于任何请求方法。浏览器应当向用户显示任何包含在此类错误响应中的实体内容。

| Http状态码 | Http Status Code                                             | Http状态码含义中文说明               |
| :--------- | :----------------------------------------------------------- | :----------------------------------- |
| **400**    | 400 Bad Request400                                           | 请求错误，通常是访问的域名未绑定引起 |
| **401**    | 401 Unauthorized401                                          | 需要身份认证验证                     |
| **402**    | 402 Payment Required                                         | -                                    |
| **403**    | 403 Forbidden403                                           | 禁止访问                             |
| **404**    | 404 Not Found404                                           | 请求的内容未找到或已删除             |
| **405**    | 405 Method Not Allowed405                                  | 不允许的请求方法                     |
| **406**    | 406 Not Acceptable406                                      | 无法响应，因资源无法满足客户端条件   |
| **407**    | 407 Proxy Authentication Required407                       | 要求通过代理的身份认证               |
| **408**    | 408 Request Timeout408                                     | 请求超时                             |
| **409**    | 409 Conflict409                                            | 存在冲突                             |
| **410**    | 410 Gone410                                                | 资源已经不存在(过去存在)             |
| **411**    | 411 Length Required411                                     | 无法处理该请求                       |
| **412**    | 412 Precondition Failed412                                 | 请求条件错误                         |
| **413**    | 413 Payload Too Large413                                   | 请求的实体过大                       |
| **414**    | 414 Request-URI Too Long414                                | 请求的URI过长                        |
| **415**    | 415 Unsupported Media Type415                              | 无法处理的媒体格式                   |
| **416**    | 416 Range Not Satisfiable416                               | 请求的范围无效                       |
| **417**    | 417 Expectation Failed417                                  | 无法满足的Expect                     |
| **418**    | 418 I'm a teapot418                                        | 愚人节笑话                           |
| **421**    | 421 There are too many connections from your internet address421 | 连接数超限                           |
| **422**    | 422 Unprocessable Entity422                                | 请求的语义错误                       |
| **423**    | 423 Locked423                                              | 当前资源被锁定                       |
| **424**    | 424 Failed Dependency424                                   | 当前请求失败                         |
| **425**    | 425 Unordered Collection425                                | 未知                                 |
| **426**    | 426 Upgrade Required426                                    | 请切换到TLS/1.0                      |
| **428**    | 428 Precondition Required428                               | 请求未带条件                         |
| **429**    | 429 Too Many Requests429                                   | 并发请求过多                         |
| **431**    | 431 Request Header Fields Too Large431                     | 请求头过大                           |
| **449**    | 449 Retry With449                                          | 请重试                               |
| **451**    | 451 Unavailable For Legal Reasons451                       | 访问被拒绝（法律的要求）             |
| **499**    | 499 Client Closed Request499                               | 客户端主动关闭了连接                 |

### 5、 HTTP Status Code 5xx 服务器错误状态

这一组状态码说明服务器在处理请求的过程中有错误或者异常状态发生，也有可能是服务器意识到以当前的软硬件资源无法完成对请求的处理。除非这是一个HEAD请求，否则服务器应当包含一个解释当前错误状态以及这个状况是临时的还是永久的解释信息实体。浏览器应当向用户展示任何在当前响应中被包含的实体。

| Http状态码 | Http Status Code                         | Http状态码含义中文说明   |
| :--------- | :--------------------------------------- | :----------------------- |
| **500**    | 500 Internal Server Error500           | 服务器端程序错误         |
| **501**    | 501 Not Implemented501                 | 服务器不支持的请求方法   |
| **502**    | 502 Bad Gateway502                     | 网关无响应               |
| **503**    | 503 Service Unavailable503             | 服务器端临时错误         |
| **504**    | 504 Gateway Timeout504                 | 网关超时                 |
| **505**    | 505 HTTP Version Not Supported505      | 服务器不支持的HTTP版本   |
| **506**    | 506 Variant Also Negotiates506         | 服务器内部配置错误       |
| **507**    | 507 Insufficient Storage507            | 服务器无法存储请求       |
| **508**    | 508 Loop Detected508                   | 服务器因死循环而终止操作 |
| **509**    | 509 Bandwidth Limit Exceeded509        | 服务器带宽限制           |
| **510**    | 510 Not Extended510                    | 获取资源策略未被满足     |
| **511**    | 511 Network Authentication Required511 | 需验证以许可连接         |
| **599**    | 599 Network Connect Timeout Error599   | 网络连接超时             |


以上即是常见HTTP状态码的分类说明。

------

CSharp 对应的 HttpStatusCode 如下：

```c#
public enum HttpStatusCode
{
        //
        // 摘要:
        //     等效于 HTTP 状态 100。System.Net.HttpStatusCode.Continue 指示客户端可能继续其请求。
        Continue = 100,
        //
        // 摘要:
        //     等效于 HTTP 状态 101。System.Net.HttpStatusCode.SwitchingProtocols 指示正在更改协议版本或协议。
        SwitchingProtocols = 101,
        //
        // 摘要:
        //     等效于 HTTP 状态 200。System.Net.HttpStatusCode.OK 指示请求成功，且请求的信息包含在响应中。这是最常接收的状态代码。
        OK = 200,
        //
        // 摘要:
        //     等效于 HTTP 状态 201。System.Net.HttpStatusCode.Created 指示请求导致在响应被发送前创建新资源。
        Created = 201,
        //
        // 摘要:
        //     等效于 HTTP 状态 202。System.Net.HttpStatusCode.Accepted 指示请求已被接受做进一步处理。
        Accepted = 202,
        //
        // 摘要:
        //     等效于 HTTP 状态 203。System.Net.HttpStatusCode.NonAuthoritativeInformation 指示返回的元信息来自缓存副本而不是原始服务器，因此可能不正确。
        NonAuthoritativeInformation = 203,
        //
        // 摘要:
        //     等效于 HTTP 状态 204。System.Net.HttpStatusCode.NoContent 指示已成功处理请求并且响应已被设定为无内容。
        NoContent = 204,
        //
        // 摘要:
        //     等效于 HTTP 状态 205。System.Net.HttpStatusCode.ResetContent 指示客户端应重置（或重新加载）当前资源。
        ResetContent = 205,
        //
        // 摘要:
        //     等效于 HTTP 状态 206。System.Net.HttpStatusCode.PartialContent 指示响应是包括字节范围的 GET 请求所请求的部分响应。
        PartialContent = 206,
        //
        // 摘要:
        //     等效于 HTTP 状态 300。System.Net.HttpStatusCode.MultipleChoices 指示请求的信息有多种表示形式。默认操作是将此状态视为重定向，并遵循与此响应关联的
        //     Location 标头的内容。
        MultipleChoices = 300,
        //
        // 摘要:
        //     等效于 HTTP 状态 300。System.Net.HttpStatusCode.Ambiguous 指示请求的信息有多种表示形式。默认操作是将此状态视为重定向，并遵循与此响应关联的
        //     Location 标头的内容。
        Ambiguous = 300,
        //
        // 摘要:
        //     等效于 HTTP 状态 301。System.Net.HttpStatusCode.MovedPermanently 指示请求的信息已移到 Location
        //     头中指定的 URI 处。接收到此状态时的默认操作为遵循与响应关联的 Location 头。
        MovedPermanently = 301,
        //
        // 摘要:
        //     等效于 HTTP 状态 301。System.Net.HttpStatusCode.Moved 指示请求的信息已移到 Location 头中指定的 URI
        //     处。接收到此状态时的默认操作为遵循与响应关联的 Location 头。原始请求方法为 POST 时，重定向的请求将使用 GET 方法。
        Moved = 301,
        //
        // 摘要:
        //     等效于 HTTP 状态 302。System.Net.HttpStatusCode.Found 指示请求的信息位于 Location 头中指定的 URI
        //     处。接收到此状态时的默认操作为遵循与响应关联的 Location 头。原始请求方法为 POST 时，重定向的请求将使用 GET 方法。
        Found = 302,
        //
        // 摘要:
        //     等效于 HTTP 状态 302。System.Net.HttpStatusCode.Redirect 指示请求的信息位于 Location 头中指定的 URI
        //     处。接收到此状态时的默认操作为遵循与响应关联的 Location 头。原始请求方法为 POST 时，重定向的请求将使用 GET 方法。
        Redirect = 302,
        //
        // 摘要:
        //     等效于 HTTP 状态 303。作为 POST 的结果，System.Net.HttpStatusCode.SeeOther 将客户端自动重定向到 Location
        //     头中指定的 URI。用 GET 生成对 Location 标头所指定的资源的请求。
        SeeOther = 303,
        //
        // 摘要:
        //     等效于 HTTP 状态 303。作为 POST 的结果，System.Net.HttpStatusCode.RedirectMethod 将客户端自动重定向到
        //     Location 头中指定的 URI。用 GET 生成对 Location 标头所指定的资源的请求。
        RedirectMethod = 303,
        //
        // 摘要:
        //     等效于 HTTP 状态 304。System.Net.HttpStatusCode.NotModified 指示客户端的缓存副本是最新的。未传输此资源的内容。
        NotModified = 304,
        //
        // 摘要:
        //     等效于 HTTP 状态 305。System.Net.HttpStatusCode.UseProxy 指示请求应使用位于 Location 头中指定的 URI
        //     的代理服务器。
        UseProxy = 305,
        //
        // 摘要:
        //     等效于 HTTP 状态 306。System.Net.HttpStatusCode.Unused 是未完全指定的 HTTP/1.1 规范的建议扩展。
        Unused = 306,
        //
        // 摘要:
        //     等效于 HTTP 状态 307。System.Net.HttpStatusCode.TemporaryRedirect 指示请求信息位于 Location
        //     头中指定的 URI 处。接收到此状态时的默认操作为遵循与响应关联的 Location 头。原始请求方法为 POST 时，重定向的请求还将使用 POST 方法。
        TemporaryRedirect = 307,
        //
        // 摘要:
        //     等效于 HTTP 状态 307。System.Net.HttpStatusCode.RedirectKeepVerb 指示请求信息位于 Location
        //     头中指定的 URI 处。接收到此状态时的默认操作为遵循与响应关联的 Location 头。原始请求方法为 POST 时，重定向的请求还将使用 POST 方法。
        RedirectKeepVerb = 307,
        //
        // 摘要:
        //     等效于 HTTP 状态 400。System.Net.HttpStatusCode.BadRequest 指示服务器未能识别请求。如果没有其他适用的错误，或者不知道准确的错误或错误没有自己的错误代码，则发送
        //     System.Net.HttpStatusCode.BadRequest。
        BadRequest = 400,
        //
        // 摘要:
        //     等效于 HTTP 状态 401。System.Net.HttpStatusCode.Unauthorized 指示请求的资源要求身份验证。WWW-Authenticate
        //     头包含如何执行身份验证的详细信息。
        Unauthorized = 401,
        //
        // 摘要:
        //     等效于 HTTP 状态 402。保留 System.Net.HttpStatusCode.PaymentRequired 以供将来使用。
        PaymentRequired = 402,
        //
        // 摘要:
        //     等效于 HTTP 状态 403。System.Net.HttpStatusCode.Forbidden 指示服务器拒绝满足请求。
        Forbidden = 403,
        //
        // 摘要:
        //     等效于 HTTP 状态 404。System.Net.HttpStatusCode.NotFound 指示请求的资源不在服务器上。
        NotFound = 404,
        //
        // 摘要:
        //     等效于 HTTP 状态 405。System.Net.HttpStatusCode.MethodNotAllowed 指示请求的资源上不允许请求方法（POST
        //     或 GET）。
        MethodNotAllowed = 405,
        //
        // 摘要:
        //     等效于 HTTP 状态 406。System.Net.HttpStatusCode.NotAcceptable 指示客户端已用 Accept 头指示将不接受资源的任何可用表示形式。
        NotAcceptable = 406,
        //
        // 摘要:
        //     等效于 HTTP 状态 407。System.Net.HttpStatusCode.ProxyAuthenticationRequired 指示请求的代理要求身份验证。Proxy-authenticate
        //     头包含如何执行身份验证的详细信息。
        ProxyAuthenticationRequired = 407,
        //
        // 摘要:
        //     等效于 HTTP 状态 408。System.Net.HttpStatusCode.RequestTimeout 指示客户端没有在服务器期望请求的时间内发送请求。
        RequestTimeout = 408,
        //
        // 摘要:
        //     等效于 HTTP 状态 409。System.Net.HttpStatusCode.Conflict 指示由于服务器上的冲突而未能执行请求。
        Conflict = 409,
        //
        // 摘要:
        //     等效于 HTTP 状态 410。System.Net.HttpStatusCode.Gone 指示请求的资源不再可用。
        Gone = 410,
        //
        // 摘要:
        //     等效于 HTTP 状态 411。System.Net.HttpStatusCode.LengthRequired 指示缺少必需的 Content-length
        //     头。
        LengthRequired = 411,
        //
        // 摘要:
        //     等效于 HTTP 状态 412。System.Net.HttpStatusCode.PreconditionFailed 指示为此请求设置的条件失败，且无法执行此请求。条件是用条件请求标头（如
        //     If-Match、If-None-Match 或 If-Unmodified-Since）设置的。
        PreconditionFailed = 412,
        //
        // 摘要:
        //     等效于 HTTP 状态 413。System.Net.HttpStatusCode.RequestEntityTooLarge 指示请求太大，服务器无法处理。
        RequestEntityTooLarge = 413,
        //
        // 摘要:
        //     等效于 HTTP 状态 414。System.Net.HttpStatusCode.RequestUriTooLong 指示 URI 太长。
        RequestUriTooLong = 414,
        //
        // 摘要:
        //     等效于 HTTP 状态 415。System.Net.HttpStatusCode.UnsupportedMediaType 指示请求是不支持的类型。
        UnsupportedMediaType = 415,
        //
        // 摘要:
        //     等效于 HTTP 状态 416。System.Net.HttpStatusCode.RequestedRangeNotSatisfiable 指示无法返回从资源请求的数据范围，因为范围的开头在资源的开头之前，或因为范围的结尾在资源的结尾之后。
        RequestedRangeNotSatisfiable = 416,
        //
        // 摘要:
        //     等效于 HTTP 状态 417。System.Net.HttpStatusCode.ExpectationFailed 指示服务器未能符合 Expect
        //     头中给定的预期值。
        ExpectationFailed = 417,
        //
        // 摘要:
        //     等效于 HTTP 状态 426。System.Net.HttpStatusCode.UpgradeRequired 指示客户端应切换为诸如 TLS/1.0
        //     之类的其他协议。
        UpgradeRequired = 426,
        //
        // 摘要:
        //     等效于 HTTP 状态 500。System.Net.HttpStatusCode.InternalServerError 指示服务器上发生了一般错误。
        InternalServerError = 500,
        //
        // 摘要:
        //     等效于 HTTP 状态 501。System.Net.HttpStatusCode.NotImplemented 指示服务器不支持请求的函数。
        NotImplemented = 501,
        //
        // 摘要:
        //     等效于 HTTP 状态 502。System.Net.HttpStatusCode.BadGateway 指示中间代理服务器从另一代理或原始服务器接收到错误响应。
        BadGateway = 502,
        //
        // 摘要:
        //     等效于 HTTP 状态 503。System.Net.HttpStatusCode.ServiceUnavailable 指示服务器暂时不可用，通常是由于过多加载或维护。
        ServiceUnavailable = 503,
        //
        // 摘要:
        //     等效于 HTTP 状态 504。System.Net.HttpStatusCode.GatewayTimeout 指示中间代理服务器在等待来自另一个代理或原始服务器的响应时已超时。
        GatewayTimeout = 504,
        //
        // 摘要:
        //     等效于 HTTP 状态 505。System.Net.HttpStatusCode.HttpVersionNotSupported 指示服务器不支持请求的
        //     HTTP 版本。
        HttpVersionNotSupported = 505
}
```

