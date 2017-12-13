# API+OAuth
## OAuth 介绍
### 名词解释
1. Third-party application：第三方应用，即Client，比如说欧冶
2. HTTP service：HTTP服务提供商，为投客。
3. Resource Owner：资源所有者，用户、API网关也为资源拥有者。
4. User Agent：用户代理，浏览器或App。
5. Authorization server：认证服务器。
6. Resource server：资源服务器，后端纯服务。API与之交互。
### 运行流程
![OAuth认证](media/15131539869801/OAuth%E8%AE%A4%E8%AF%81.jpg)
1. 用户打开客户端以后，客户端要求用户给予授权。
2. 用户同意给予客户端授权。
3. 客户端使用上一步获得的授权，向认证服务器申请令牌。
4. 认证服务器对客户端进行认证以后，确认无误，同意发放令牌。
5. 客户端使用令牌，向资源服务器申请获取资源。
6. 资源服务器确认令牌无误，同意向客户端开放资源。
### 客户端的授权模式
客户端必须得到用户的授权（authorization grant），才能获得令牌（access token）。OAuth 2.0定义了四种授权方式。
* 授权码模式（authorization code）
* 简化模式（implicit）
* 密码模式（resource owner password credentials）
* 客户端模式（client credentials）
### 授权码模式(Authorization Code Mode)
授权码模式（authorization code）是功能最完整、流程最严密的授权模式。它的特点就是通过客户端的后台服务器，与"服务提供商"的认证服务器进行互动。
![OAuth授权码模式](media/15131539869801/OAuth%E6%8E%88%E6%9D%83%E7%A0%81%E6%A8%A1%E5%BC%8F.jpg)
需要在认证中心中注册appid，提交redirect_uri，获取颁发的appsecret，该redirect_uri负责接收code换取access_token，redirect_uri通过302跳转。直接返回access_token有可能被黑客拦截到，而拿到access_token就可以获得用户信息了，非常不安全。不是所有的网站都支持https的，通过redirect_uri这时候再去获取access_token，就是https请求了，因为换取access_token的认证中心是https的。黑客拿到了code，没有之前颁发的appsecret就换取不到accesstoken。
### 简化模式(Implicit Grant Type)
![OAuth简化模式](media/15131539869801/OAuth%E7%AE%80%E5%8C%96%E6%A8%A1%E5%BC%8F.jpg)

### 客户端模式(Client Credentials Grant)
![OAuth客户端模式](media/15131539869801/OAuth%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%A8%A1%E5%BC%8F.jpg)
## Restful API设计
### 协议
支持标准的http2.0与https
###域名
https://api.touker.com
### 版本
https://api.touker.com/v1
### 路径(Endpoint)
路径又称"终点"（endpoint），表示API的具体网址。
在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。
举例来说，有一个API提供用户（user）服务，包含各种信息查询：
* https://api.touker.com/v1/user/user_info
### HTTP动词
对于资源的具体操作类型，由HTTP动词表示。
常用的HTTP动词有下面五个（括号里是对应的SQL命令）。
* GET（SELECT）：从服务器取出资源（一项或多项）。
* POST（CREATE）：在服务器新建一个资源。
* PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
* PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
* DELETE（DELETE）：从服务器删除资源。
还有两个不常用的HTTP动词。
* HEAD：获取资源的元数据。
* OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。
例子：
* GET /user/user_info：获取用户信息
* POST /user：新注册用户
* PUT /user/user_info 更新用户信息，整个信息重新提交
* PATCH /user/user_info 更新用户信息，更新个别属性
### 更多
[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
### API层设计
![API设计](media/15131539869801/API%E8%AE%BE%E8%AE%A1.jpg)
从app的角度，app开发者更希望提供面向page的接口，即一个接口便能获取到该得的数据，而站在后端开发者的角度，更希望提供原子性的数据，而不是拼凑一些乱七八糟的数据。如何平衡两者？分层架构，简单地分为原子数据接口层 和 中间业务层 和 gateway。
* API-function原子数据接口层：只出最原子的数据，譬如获取用户信息，获取历史活动，2个接口完全独立，不冲突，不交叉。
* API-bussiness中间业务层：负责整合，拼凑原子数据接口出来的数据,根据app的需求，把多个原子数据接口整合为一个接口输出给app。 考虑到app发版的成本较高，改动代码后发版周期很长，所以在这层也可以做一些定制化的业务逻辑。
* API-Getway gateway：经过中间业务层的数据，必须经过gateway平台作为统一入口和出口，另外gateway由于是整个api的入口，也适合做接口鉴权，防刷，作弊等业务逻辑。

## OpenAPI-授权码模式
![OpenAPI-认证-授权码模式](media/15131539869801/OpenAPI-%E8%AE%A4%E8%AF%81-%E6%8E%88%E6%9D%83%E7%A0%81%E6%A8%A1%E5%BC%8F.jpg)




