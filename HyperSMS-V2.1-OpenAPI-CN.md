# HyperSMS V2.1 OpenAPI 接口说明文档

## 1 前置说明

### 1.1 术语介绍
名词			 |说明	    |备注                
:----:		 |:----	    |:---
app        |应用接入方	|  HyperSMS OpenAPI的应用接入方
appId        |应用标识	| 开户后提供
appKey		 |应用密钥	| 开户后提供
appSecret    |应用密码	| 开户后提供
token    |服务端令牌	| 调用登陆接口后生成
callbackApproveUrl        |审核状态回执地址	| 开户前由接入方提供
callbackSendUrl        |发送状态回执地址	| 开户前由接入方提供


### 1.1 开户流程
 
1. 接入方提供 callbackApproveUrl/callbackSendUrl 两个回执地址

2. 在 HyperSMS 平台内申请开户，并生成 appId/appKey/appSecret 提供给接入方


### 1.2 接口调用流程
1. 应用接入方通过 appId/appSecret, 调用登陆接口获取token

2. 通过在头部中指定 token; 再按需调用其它接口

3. 各业务接口中调用时都需要指定sign值，计算流程 详见"附录G" 

4. 若需要开启 Ride 模式请求，请求格式 详见"2.4.3" (V2.1.safe)

5. 若业务数据需要审核，审核状态会通过 callbackApproveUrl 地址进行回执

6. 发送任务处理后，DR及Retrieve状态会通过 callbackSendUrl 地址进行回执

### 1.3 统一访问入口：

- Test: http://client.vn-vtt-test.corporfountain.com/openapi/v2/
- Prod: https://openapi.viettel.hypersms.vn/v2/

注: 上述地址可能会变，烦请跟服务方确认!

## 2 标准及规范说明

### 2.1 通信协议

HTTPS/HTTP

### 2.2 请求方法

所有接口统一使用 POST[raw] 方式，数据格式统一为JSON

### 2.3 字符编码
统一为 UTF-8 字符集编码

### 2.4 API格式标准

#### 2.4.1 Headers 标准格式
``` JSON
{
 "Content-Type":"application/json",
 "appId": 0101010,
 "timestamp": 1621503576000,
 "isTokenRide": false,
 "token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxMDAwMDE2ODMiLCJpYXQiOjE2MjE0OT"
}
```
说明:

参数			 |是否必填	|说明                
:----:		 |:----	    |:---
Content-Type |Optional	|请求类型，暂只支持 application/json
appId		 |Required	|接入方应用标识
token		 |Required	|登录后token，没有登录则为空字符串
isTokenRide	 |Optional	|Ride请求模式(true/false);默认为 false (V2.1.safe)
timestamp	 |Required	|当前UNIX时间戳(毫秒)

#### 2.4.2  Request Body 标准格式
``` JSON
{
 "key1" : "Value1",
 "key2" : "Value2"
 "timestamp": 1621503576000,
 "sign": "600e5d07f445be432d86d9271fb1df9b1"
}
```
说明:

参数			 |是否必填	|说明                
:----:		 |:----	    |:---
< key1 >  | 	|举例说明，各业务接口中的实际定义参数key
sign		 |Required	| 本次请求sign值
timestamp	 |Required	|当前UNIX时间戳(毫秒)

#### 2.4.3  "Ride模式"请求标准格式 (V2.3.2.brand)
- "Ride模式"主要是适配不同环境下的网络安全情况，支持明文及Base64加密两种请求方式
- 若需要开启 "Ride模式"，头部必须指定isTokenRide=true

##### 2.4.3.1 明文请求
``` JSON
{
 "data" : "{\"key1\":\"Value1\",\"key2\":\"Value2\"\"timestamp\":1621503576000,\"isTokenRide\":false\"sign\":\"600e5d07f445be432d86d9271fb1df9b1\"}"
}
```
说明:

参数			 |是否必填	|说明                
:----:		 |:----	    |:---
data  |Required	| Request Body JSON 字符串明文

##### 2.4.3.2 Base64加密请求
``` JSON
{
 "data" : "eyJrZXkxIjoiVmFsdWUxIiwia2V5MiI6IlZhbHVlMiIidGltZXN0YW1wIjoxNjIxNTAzNTc2MDAwLCJpc1Rva2VuUmlkZSI6ZmFsc2Uic2lnbiI6IjYwMGU1ZDA3ZjQ0NWJlNDMyZDg2ZDkyNzFmYjFkZjliMSJ9"
}
```
说明:

参数			 |是否必填	|说明                
:----:		 |:----	    |:---
data  |Required	|  Base64加密之后的 Request Body JSON字符串; JSON字符串为UTF-8编码，无须"\"转义

#### 2.4.4  Response Body 标准格式
``` JSON
{
 "code" : "100",
 "msg" : "api.response.code.success",
 "msgArgs": null,
 "data": {
    "code" : "123123123"
  }
}
```
说明:

参数			 |是否必填	|说明                
:----:		 |:----	    |:---
code  |Required	|接口响应状态
msg		 |Required	|接口响应消息;
msgArgs	 |Optional	|接口响应消息扩展描述;
data     |Required	|接口响应返回值(注:不同的接口返回值类型不同);


## 3. 鉴权相关API

### 3.1 登陆接口
- **接口说明：** 接入方通过登陆接口，获取全局token
- **接口地址：** app/login

#### 3.1.1 请求参数
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;appId				|string		| Required	| 接入方应用标识
&emsp;appSecret			|string		| Required | 接入方密码


请求示例：

``` JSON
{
  "appId":"18520322032",
  "appSecret":"acb000000"
}

```


#### 3.1.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见 "附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;expire				|long		|Required		|过期时间(单位秒)
&emsp;token				    |string		|Required		|访问令牌

示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "expire": 604800,
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxMDAwMDE2ODMiLCJpYXQiOjE2MjE1NjQ0OTAsImV4cCI6MTYyMjE2OTI5MH0.O1sqK5jk-wI2DQNOsunZ5cU0x9369cTKxItmfPRHA57Kk5ynhSoWnV5YyysX65l4LqsUKITEBUWnZ78h-UdrJA"
  }
}
```


## 4. Brandname 相关API

### 4.1 新增品牌接口
- **接口说明：** 新增品牌信息
- **接口地址：** app/brandname/add

#### 4.1.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;brandName				|string		| Required	| 品牌名称
&emsp;description			|string		| Optional | 描述
&emsp;certified			|string		| Optional | 证书URL
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)


请求示例：

``` JSON
{
  "brandName":"Google",
  "description":" Description ",
  "certified":"http://www.google.cn/aaaqeqeqw.gif",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.1.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|品牌编码


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```

### 4.2 分页查询品牌接口
- **接口说明：** 查询品牌信息,支持分页
- **接口地址：** app/brandname/query

#### 4.2.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;brandName				|string		| Optional	| 品牌名称
&emsp;code			|string		| Optional | 品牌编码
&emsp;currPage			|int		| Required | 当前页码, 默认值为 1
&emsp;pageSize			|int		| Required | 每页条数, 默认值为 10
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)


请求示例：

``` JSON
{
  "brandName":"",
  "code":"",
  "currPage": 1,
  "pageSize": 1,
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.2.2 返回结果

参数名称						|类型		|是否必填	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码 详见:"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;totalCount				    |int		|Required		|总条数
&emsp;totalPage				    |int		|Required		|总页数
&emsp;list				    |array		|Optional		|查询结果
&emsp;&emsp;code				    |string		|Required		|品牌编码
&emsp;&emsp;brandName				|string		| Required	| 品牌名称
&emsp;&emsp;certified			|string		| Optional | 证书URL
&emsp;&emsp;createTime			|long		| Optional | 创建时间戳(毫秒)
&emsp;&emsp;status			|int		| Required | 数据状态 详见:附录C


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "totalCount": 100,
    "totalPage": 2,
    "currPage": 1,
    "pageSize": 1,
    "list": [
       {
          "brandName":"bName",
          "code":"XXX13123",
          "brandName": "http://www.google.cn/aaaqeqeqw.gif",
          "createTime": 1621503576000,
          "status": 1
       }
    ]
  }
}
```

### 4.3 查询品牌明细接口
- **接口说明：** 查询品牌详情信息,包括最近一次审核信息等
- **接口地址：** app/brandname/info

#### 4.3.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code			|string		| Required | 品牌编码


请求示例：

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.3.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|品牌编码
&emsp;brandName				|string		| Required	| 品牌名称
&emsp;certified			|string		| Optional | 证书URL
&emsp;createTime			|long		| Optional | 创建时间戳(毫秒)
&emsp;status			|int		| Required | 数据状态 详见"附录D"
&emsp;approve			|object		| Optional | 审核信息
&emsp;&emsp;status			|int		| Optional | 审核状态 详见"附录E"
&emsp;&emsp;opinion			|string		| Optional | 审核意见


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
      "brandName":"bName",
      "code":"XXX13123",
      "brandName": "http://www.google.cn/aaaqeqeqw.gif",
      "createTime": 1621503576000,
      "status": 1,
      "approve": {
         "status" : 0,
         "opinion" : "pass"
      }
  }
}
```

### 4.4 品牌失效接口
- **接口说明：** 品牌数据下线并置失效
- **接口地址：** app/brandname/invalid

#### 4.4.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code			|string		| Required | 品牌编码


请求示例：

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.4.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



## 5. Template 相关API

### 5.1 新增模板接口
- **接口说明：** 新增模板信息
- **接口地址：** app/template/add

#### 5.1.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;type				|int		| Required	|产品类型；详见"附录B"
&emsp;brandNameCode			|string		| Required | 所属品牌编码
&emsp;productItemCode			|string		| Required | 所属产品项；详见"附录C"
&emsp;title			|string		| Required | 模板标题
&emsp;content			|string		| Required | 模板内容(支持最多5个动参{0}格式)



请求示例:

- **普通模板** 

``` JSON
{
  "type":2,
  "brandNameCode":"001aaab",
  "productItemCode":"SMS_AD_GROUP_1",
  "title":"temp title",
  "content":"Hi, My name is HAHA",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

- **动参模板** 

``` JSON
{
  "type":2,
  "brandNameCode":"001aaab",
  "productItemCode":"SMS_AD_GROUP_1",
  "title":"temp title",
  "content":"Hi, My name is {0}",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 5.1.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|模板编码


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```


### 5.2 编辑模板接口
- **接口说明：** 编辑模板信息
- **接口地址：** app/template/edit

#### 5.2.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code				    |string		|Required		|模板编码
&emsp;type				|int		| Optional	|产品类型；详见"附录B"
&emsp;brandNameCode			|string		| Optional | 所属品牌编码
&emsp;productItemCode			|string		| Optional | 所属产品项；详见"附录C"
&emsp;title			|string		| Optional | 模板标题
&emsp;content			|string		| Optional | 模板内容(支持最多5个动参{index}格式)


请求示例:

``` JSON
{
  "code":"1212300",
  "title":"New Temp title",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 5.2.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|模板编码


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```


### 5.3 分页查询模板接口
- **接口说明：** 按指定条件查询模板数据,支持分页
- **接口地址：** app/template/query

#### 5.3.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code				    |string		|Optional		|模板编码
&emsp;type				|int		| Optional	|产品类型；详见"附录B"
&emsp;brandNameCode			|string		| Optional | 所属品牌编码
&emsp;productItemCode			|string		| Optional | 所属产品项；详见"附录C"
&emsp;title			|string		| Optional | 模板标题(支持模糊查)
&emsp;currPage			|int		| Required | 当前页码, 默认值为 1
&emsp;pageSize			|int		| Required | 每页条数, 默认值为 10


请求示例：

``` JSON
{
  "brandNameCode":"",
  "code":"",
  "currPage": 1,
  "pageSize": 1,
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 5.3.2 返回结果

参数名称						|类型		|是否必填	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码 详见:"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;totalCount				    |int		|Required		|总条数
&emsp;totalPage				    |int		|Required		|总页数
&emsp;list				    |array		|Optional		|查询结果
&emsp;&emsp;code				    |string		|Required		|模板编码
&emsp;&emsp;type				|int		| Required	|模板类型；详见"附录B"
&emsp;&emsp;brandNameCode				|string		| Required	| 所属品牌编码
&emsp;&emsp;productItemCode			|string		| Optional | 所属产品项；详见"附录C"
&emsp;&emsp;title			|string		| Optional | 模板标题
&emsp;&emsp;createTime			|long		| Optional | 创建时间戳(毫秒)
&emsp;&emsp;status			|int		| Required | 数据状态 详见"附录D"


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "totalCount": 100,
    "totalPage": 2,
    "currPage": 1,
    "pageSize": 1,
    "list": [
       {
          "type":2,
          "brandNameCode":"001aaab",
          "productItemCode":"SMS_AD_GROUP_1",
          "title":"temp title",
          "createTime": 1621503576000,
          "status": 1
       }
    ]
  }
}
```


### 5.4 模板明细接口
- **接口说明：** 根据模板code 查询模板详情，包括审核信息
- **接口地址：** app/template/info

#### 5.4.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;code				    |string		|Required		|模板编码
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)


请求示例:

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 5.4.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|模板编码
&emsp;type				|int		| Required	|模板类型；详见"附录B"
&emsp;brandNameCode				|string		| Required	| 所属品牌编码
&emsp;productItemCode			|string		| Required | 所属产品项；详见"附录C"
&emsp;title			|string		| Required | 模板标题
&emsp;content			|string		| Required | 模板内容
&emsp;createTime			|long		| Required | 创建时间戳(毫秒)
&emsp;status			|int		| Required | 数据状态 详见"附录D"
&emsp;approve			|object		| Optional | 审核信息
&emsp;&emsp;status			|int		| Required | 审核状态 详见"附录E"
&emsp;&emsp;opinion			|string		| Optional | 审核意见


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
      "type":2,
      "brandNameCode":"001aaab",
      "productItemCode":"SMS_AD_GROUP_1",
      "title":"temp title",
      "createTime": 1621503576000,
      "status": 1,
      "content":"Hi, My name is HAHA"
      "approve": {
         "status" : 0,
         "opinion" : "pass"
      }
  }
}
```

### 5.5 模板失效接口
- **接口说明：** 模板数据下线并置失效
- **接口地址：** app/template/invalid

#### 5.3.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;code			|string		| Required | 模板编码
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)


请求示例：

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 5.3.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



## 6. Send 发送相关API

### 6.1 发送接口
- **接口说明：** 发送单个号码
- **接口地址：** app/send

#### 6.1.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;type				|int		| Required	|产品类型；详见"附录B"
&emsp;templateCode			|string		| Optional | HyperSMS模板编码 [V2.0版本暂不支持]
&emsp;smsTemplateCode			|string		| Required | SMS模板编码 
&emsp;subId			|string		| Required | 发送号码
&emsp;param			|array		| Optional | 动参值集合 注:顺序要跟模板中动参{index}对应上
&emsp;encodeType			|int		| Optional | 可选(0:明文,1:运营商加密) 默认为0


请求示例：

- **普通模板** 

``` JSON
{
  "type":2,
  "smsTemplateCode":"001aaab",
  "subId":"091313123123",
  "encodeType": 0,
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

- **动参模板** 

``` JSON
{
  "type":2,
  "smsTemplateCode":"001aaab",
  "subId":"091313123123",
  "param": ["0","2"]
  "encodeType": 0,
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 6.1.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|发送编码
&emsp;status			|int		|Optional		|发送状态(1:正常;0:失败)
&emsp;msg				|string		|Optional		|发送详情


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "160139858512181547",
    "status": 1
    "msg": null
  }
}
```


### 6.2 批量发送接口
- **接口说明：** 发送多个号码(暂只支持500个号码)
- **接口地址：** app/send/batch

#### 6.2.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;type				|int		| Required	|产品类型；详见"附录B"
&emsp;templateCode			|string		| Optional | HyperSMS模板编码 [V2.0版本暂不支持]
&emsp;smsTemplateCode			|string		| Required | SMS模板编码 
&emsp;subList			|array		| Required | 发送多个号码
&emsp;&emsp;subId			|string		| Required | 发送号码
&emsp;&emsp;param			|array		| Optional | 动参值集合 注:顺序要跟模板中动参{index}对应上
&emsp;&emsp;encodeType			|int		| Optional | 可选(0:明文,1:运营商加密) 默认为0


请求示例：

- **普通模板** 

``` JSON
{
    "type":2,
    "smsTemplateCode":"001aaab",
    "subList":[
        {
            "subId":"091313123121"
        },
        {
            "subId":"091313123122"
        }
    ],
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

- **动参模板** 

``` JSON
{
  "type":2,
  "smsTemplateCode":"001aaab",
  "subList":[
        {
         "subId":"091313123121",
         "param": ["0","2"]
        },
        {
          "subId":"091313123122",
          "param": ["0","2"]
        }
  ],
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 6.2.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;sendList						|array		|Required		|&nbsp;
&emsp;&emsp;code				    |string		|Required		|发送编码
&emsp;&emsp;status			|int		|Optional		|发送状态(1:正常;0:失败)
&emsp;&emsp;msg				|string		|Optional		|发送详情


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "sendList":[
        {
          "code": "160139858512181547",
          "status": 1,
          "msg": null
        },
        {
          "code": "160139858512181548",
          "status": 1,
          "msg": null
      }
    ]
  },
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}
```

## 7. Campaign 相关API

### 7.1 创建活动接口
- **接口说明：** 创建活动任务信息及发送规则
- **接口地址：** app/campaign/add

#### 7.1.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;type				|int		| Required	|产品类型；详见"附录B"
&emsp;templateCode			|string		| Optional | HyperSMS模板编码 [V2.0版本暂不支持]
&emsp;smsTemplateCode			|string		| Required | SMS模板编码 
&emsp;name				|string		| Required	| 活动名称
&emsp;description			|string		| Optional | 活动描述
&emsp;property			|object		| Optional | 活动描述
&emsp;&emsp;startTime			|string		| Required | 活动开始时间(格式 yyyy-MM-dd HH:mm:ss)
&emsp;&emsp;endTime			|string		| Required | 活动结束时间 (格式 yyyy-MM-dd HH:mm:ss)
&emsp;&emsp;sendPeriod			|array		| Optional | 限制发送区间集
&emsp;&emsp;&emsp;start			|string		| Required | 时间段起始(格式 HH:mm:ss) 
&emsp;&emsp;&emsp;end			|string		| Required | 时间段结尾(格式 HH:mm:ss) 
&emsp;&emsp;subList			|array		| Required |&nbsp; 最多 500 个号码
&emsp;&emsp;&emsp;subId			|string		| Required | 发送号码
&emsp;&emsp;&emsp;encodeType	|int		| Optional | 可选(0:明文,1:运营商加密) 默认为0


请求示例：

``` JSON
{
  "type":2,
  "smsTemplateCode": "001aaab",
  "name": "Campaign Test",
  "description": " description ",
  "property":{
       "startTime": "2021-05-21 12:12:12",
       "endTime": "2021-05-22 12:12:12",
       "sendPeriod": [
           {
            "start": "12:12:12",
            "end": "15:12:12"
           }   
       ],
       "subList":[
           {
             "code": "160139858512181547",
             "status": 1,
             "msg": null
           },
           {
             "code": "160139858512181548",
             "status": 1,
             "msg": null
           }
       ]
  },
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.1.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|活动编码


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```



### 7.2 编辑活动接口
- **接口说明：** 编辑活动任务及活动规则
- **接口地址：** app/campaign/edit

#### 7.2.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code				    |string		|Required		|活动编码
&emsp;type				|int		| Optional	|产品类型；详见"附录B"
&emsp;templateCode			|string		| Optional | HyperSMS模板编码 [V2.0版本暂不支持]
&emsp;smsTemplateCode			|string		| Optional | SMS模板编码 
&emsp;name				|string		| Optional	| 活动名称
&emsp;description			|string		| Optional | 活动描述
&emsp;property			|object		| Optional | 活动描述
&emsp;&emsp;startTime			|string		| Optional | 活动开始时间(格式 yyyy-MM-dd HH:mm:ss)
&emsp;&emsp;endTime			|string		| Optional | 活动结束时间 (格式 yyyy-MM-dd HH:mm:ss)
&emsp;&emsp;sendPeriod			|array		| Optional | 限制发送区间集
&emsp;&emsp;&emsp;start			|string		| Optional | 时间段起始(格式 HH:mm:ss) 
&emsp;&emsp;&emsp;end			|string		| Optional | 时间段结尾(格式 HH:mm:ss) 
&emsp;&emsp;subList			|array		| Optional |&nbsp; 最多 500 个号码
&emsp;&emsp;&emsp;subId			|string		| Optional | 发送号码
&emsp;&emsp;&emsp;encodeType	|int		| Optional | 可选(0:明文,1:运营商加密) 默认为0


请求示例：

``` JSON
{
  "code":"1212300",
  "type":2,
  "smsTemplateCode": "001aaab",
  "name": "Campaign Test",
  "description": " description ",
  "property":{
       "startTime"："2021-05-21 12:12:12",
       "endTime"："2021-05-22 12:12:12",
       "sendPeriod"：[
          {
            "start": "12:12:12",
            "end": "15:12:12"
          }      
       ],
       "subList":[
          {
            "code": "160139858512181547",
            "status": 1,
            "msg": null
          },
          {
            "code": "160139858512181548",
            "status": 1,
            "msg": null
          }
       ]
  },
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.2.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|活动编码


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```

### 7.3 分页查询活动接口
- **接口说明：** 按指定条件查询活动数据,支持分页
- **接口地址：** app/campaign/query

#### 7.3.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code				    |string		|Optional		|活动编码
&emsp;type				|int		| Optional	|产品类型；详见"附录B"
&emsp;templateCode			|string		| Optional | HyperSMS模板编码 [V2.0版本暂不支持]
&emsp;smsTemplateCode			|string		| Optional | SMS模板编码 
&emsp;name			|string		| Optional | 活动名称(支持模糊查)
&emsp;currPage			|int		| Required | 当前页码, 默认值为 1
&emsp;pageSize			|int		| Required | 每页条数, 默认值为 10


请求示例：

``` JSON
{
  "smsTemplateCode":"",
  "code":"",
  "currPage": 1,
  "pageSize": 1,
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.3.2 返回结果

参数名称						|类型		|是否必填	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码 详见:"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;totalCount				    |int		|Required		|总条数
&emsp;totalPage				    |int		|Required		|总页数
&emsp;list				    |array		|Required		|查询结果
&emsp;&emsp;code				    |string		|Required		|活动编码
&emsp;&emsp;type				|int		| Required	|产品类型；详见"附录B"
&emsp;&emsp;templateCode			|string		| Optional | HyperSMS模板编码 [V2.0版本暂不支持]
&emsp;&emsp;smsTemplateCode			|string		| Optional | SMS模板编码 
&emsp;&emsp;name				|string		| Required	| 活动名称
&emsp;&emsp;description			|string		| Optional | 活动描述
&emsp;&emsp;createTime			|long		| Required | 创建时间戳(毫秒)
&emsp;&emsp;status			|int		| Required | 数据状态 详见"附录D"


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "totalCount": 100,
    "totalPage": 2,
    "currPage": 1,
    "pageSize": 1,
    "list": [
       {
          "code": "1212300",
          "type": 2,
          "smsTemplateCode": "001aaab",
          "name": "Campaign Test",
          "description": " description ",
          "createTime": 1621503576000,
          "status": 1
       }
    ]
  }
}
```


### 7.4 活动明细接口
- **接口说明：** 根据活动 code 查询活动详情，包括活动基本信息、规则信息、发送目标及审核信息
- **接口地址：** app/campaign/info

#### 7.4.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code				    |string		|Required		|活动编码


请求示例:

``` JSON
{
  "code":"001aaab",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 7.4.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;
&emsp;code				    |string		|Required		|活动编码编码
&emsp;type				|int		| Required	|产品类型；详见"附录B"
&emsp;templateCode			|string		| Optional | HyperSMS模板编码 [V2.0版本暂不支持]
&emsp;smsTemplateCode			|string		| Optional | SMS模板编码 
&emsp;name				|string		| Required	| 活动名称
&emsp;description			|string		| Optional | 活动描述
&emsp;createTime			|long		| Required | 创建时间戳(毫秒)
&emsp;status			|int		| Required | 活动数据状态 详见"附录D"
&emsp;property			|object		| Optional | 活动规则描述
&emsp;&emsp;startTime			|string		| Optional | 活动开始时间(格式 yyyy-MM-dd HH:mm:ss)
&emsp;&emsp;endTime			|string		| Optional | 活动结束时间 (格式 yyyy-MM-dd HH:mm:ss)
&emsp;&emsp;sendPeriod			|array		| Optional | 限制发送区间集
&emsp;&emsp;&emsp;start			|string		| Optional | 时间段起始(格式 HH:mm:ss) 
&emsp;&emsp;&emsp;end			|string		| Optional | 时间段结尾(格式 HH:mm:ss) 
&emsp;approve			|object		| Optional | 审核信息
&emsp;&emsp;status			|int		| Required | 审核状态 详见"附录E"
&emsp;&emsp;opinion			|string		| Optional | 审核意见
&emsp;sendList						|array		|Required		|发送号码信息
&emsp;&emsp;code				    |string		|Required		|发送编码
&emsp;&emsp;status			|int		|Optional		|发送状态(1:正常;0:失败)
&emsp;&emsp;msg				|string		|Optional		|发送详情

示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
      "code": "1212300",
      "type": 2,
      "smsTemplateCode": "001aaab",
      "name": "Campaign Test",
      "description": " description ",
      "createTime": 1621503576000,
      "status": 1
      "property": {
         "startTime": "2021-05-21 12:12:12",
         "endTime": "2021-05-22 12:12:12",
         "sendPeriod"：[
            {
              "start": "12:12:12",
              "end": "15:12:12"
            }
         ]
       },
      "approve": {
         "status" : 0,
         "opinion" : "pass"
      },
      "sendList":[
         {
           "code": "160139858512181547",
           "status": 1,
           "msg": null
         },
         {
           "code": "160139858512181548",
           "status": 1,
           "msg": null
         }
      ]
  }
}
```

### 7.5 活动失效接口
- **接口说明：** 失效活动(注：正在执行中的活动不能失效)
- **接口地址：** app/campaign/invalid

#### 7.5.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code			|string		| Required | 活动编码


请求示例：

``` JSON
{
  "code":"001aaab",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.5.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```


### 7.6 取消活动任务接口
- **接口说明：** 取消正在执行中的活动任务
- **接口地址：** app/campaign/cancel

#### 7.6.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code			|string		| Required | 活动编码


请求示例：

``` JSON
{
  "code":"001aaab",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.6.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```


### 7.7 暂停活动任务接口
- **接口说明：** 暂停正在执行中的活动任务
- **接口地址：** app/campaign/pause

#### 7.7.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code			|string		| Required | 活动编码


请求示例：

``` JSON
{
  "code":"001aaab",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.7.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



### 7.8 恢复活动任务接口
- **接口说明：** 恢复正在暂停中的活动任务
- **接口地址：** app/campaign/resume

#### 7.8.1 请求参数
  
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;code			|string		| Required | 活动编码


请求示例：

``` JSON
{
  "code":"001aaab",
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.8.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见"附录A"
msg							|string		|Required		|&nbsp;
data						|object		|Required		|&nbsp;


示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



## 8. 回执相关API

### 8.1 审核状态回执接口
- **接口说明：** 需要审核的数据，审核状态变更时会通过 接入方提供的 callbackApproveUrl 进行回执
- **接口地址：** 接入方提供

#### 8.1.1 请求参数
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;type				|int		| Required	| 回执类型 1:Brandname 2:Template 3:Campaign
&emsp;code			|string		| Required | 回执编码
&emsp;approve			|object		| Required | 回执审核信息
&emsp;&emsp;status		|int		| Required | 审核状态 详见"附录E"
&emsp;&emsp;opinion		|string		| Optional | 审核意见


请求示例：

``` JSON
{
  "sign": "afsdfasdf2asdfasdf",
  "timestamp": 1621503576000,
  "type": 1,
  "code":"acb000000",
  "approve": {
     "status" : 1,
     "opinion" : "pass"
  }
}

```


#### 8.1.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见 "附录A"
msg							|string		|Required		|&nbsp;

示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
}
```



### 8.2 发送、下载状态回执接口
- **接口说明：** 发送任务执行后，发送、下载状态会通过接入方指定的 callbackSendUrl 进行回执
- **接口地址：** 接入方提供

#### 8.2.1 请求参数
参数名称					|类型		|是否必填	 |描述  
:----					|:---		|:------ |:---
&emsp;sign		|string		| Optional  | 签名 详见"附录G"
timestamp	 |long	|Required   |当前UNIX时间戳(毫秒)
&emsp;type				|int		| Required	| 回执类型 0:DR 1:Retrieve
&emsp;code			|string		| Required | 回执编码
&emsp;status			|int		| Required | 回执状态 详见"附录F"


请求示例：

``` JSON
{
  "sign": "afsdfasdf2asdfasdf",
  "timestamp": 1621503576000,
  "type": 1,
  "code":"acb000000",
  "status": 1
}

```


#### 8.2.2 返回结果

参数名称						|类型		|出现要求	|描述  
:----						|:---		|:------	|:---	
code						|int		|Required		|响应码，详见 "附录A"
msg							|string		|Required		|&nbsp;

示例：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
}
```



## 附录

### 附录A 响应码说明

统一响应码	|响应明细         |说明
:----	|:---                  |:---
0		|api.response.code.success   |请求成功
&emsp;  |&emsp;                    |&emsp;
-1		|api.response.code.fail  |请求失败
-1		|api.response.code.illegal |请求非法
-1		|api.response.code.unknownError |服务器内部异常
-1		|api.response.code.paramSignError |签名错误
&emsp;  |&emsp;                    |&emsp;
100		|api.response.code.paramError    |参数错误
100		|api.response.code.paramTypeError |参数类型错误
100		|api.response.code.paramNotEmpty  |参数为空
100		|api.response.code.paramTokenInvalid  |参数token失效
&emsp;  |&emsp;                    |&emsp;
200		|api.response.code.user.accountNotExist |appId 或 appSecret 错误
200		|api.response.code.user.accountHasLocked |帐户已锁


### 附录B 产品类型编码说明

类型		|名称
:----	|:---
1		| HyperSMS
2		| SMS

注: v2.0 版本暂时只支持 SMS；

### 附录C 产品项编码说明

产品项编码	|类型		|catid  	|Group Name | 说明 
:----	|:--- |:--- |:---|:---
SMS_AD_GROUP_1_EDU	|	Advertisement	|	SMS_EDU_QC	|	Group 1	|	Tuyển dụng, tuyển sinh, giáo dục
SMS_AD_GROUP_2_BDS	|	Advertisement	|	SMS_BDS_QC	|	Group 2	|	Bất động sản
SMS_AD_GROUP_3_COS	|	Advertisement	|	SMS_COS_QC	|	Group 3	|	Hóa mỹ phẩm, làm đẹp
SMS_AD_GROUP_3_GTRI	|	Advertisement	|	SMS_GTRI_QC	|	Group 3	|	Giải trí
SMS_AD_GROUP_3_FSION	|	Advertisement	|	SMS_FSION_QC	|	Group 3	|	Thời trang
SMS_AD_GROUP_3_FOOD	|	Advertisement	|	SMS_FOOD_QC	|	Group 3	|	Thực phẩm, đồ uống, y tế - dược
SMS_AD_GROUP_3_MART	|	Advertisement	|	SMS_MART_QC	|	Group 3	|	Siêu thị, Trung tâm thương mại
SMS_AD_GROUP_3_TRAN	|	Advertisement	|	SMS_TRAN_QC	|	Group 3	|	Vận tải, vận chuyển
SMS_AD_GROUP_3_FIN	|	Advertisement	|	SMS_FIN_QC	|	Group 3	|	Tài chính ngân hàng
SMS_AD_GROUP_3_TMDT	|	Advertisement	|	SMS_TMDT_QC	|	Group 3	|	Thương mại điện tử
SMS_AD_GROUP_3_DL	|	Advertisement	|	SMS_DL_QC	|	Group 3	|	Du lịch
SMS_AD_GROUP_3_ELEC	|	Advertisement	|	SMS_ELEC_QC	|	Group 3	|	Điện, nước, xăng dầu
SMS_AD_GROUP_4_OTHER	|	Advertisement	|	SMS_OTHER_QC	|	Group 4	|	Nhóm thuộc lĩnh vực khác
SMS_CSKH_GROUP_1_FIN	|	Customer Care	|	SMS_FIN_CSKH	|	Group 1	|	Nhắn tin thuộc lĩnh vực chứng khoán, tài chính.
SMS_CSKH_GROUP_2.2_HCHINH	|	Customer Care	|	SMS_HCHINH_CKSH	|	Group 2.2	|	lực lượng vũ trang, hành chính công, đơn vị sự nghiệp, đoàn thể.
SMS_CSKH_GROUP_KCN_INDUS	|	Customer Care	|	SMS_INDUS_CSKH	|	Group KCN	|	doanh nghiệp thuộc khu công nghiệp
SMS_CSKH_GROUP_3.3_OTTIN	|	Customer Care	|	SMS_OTTIN_CSKH	|	Group 3.3	|	lĩnh vực OTT, mạng xã hội quốc tế
SMS_CSKH_GROUP_3.4_OTTL	|	Customer Care	|	SMS_OTTL_CSKH	|	Group 3.4	|	lĩnh vực OTT, mạng xã hội trong nước
SMS_CSKH_GROUP_4_EWP	|	Customer Care	|	SMS_EWP_CSKH	|	Group 4	|	ngành điện, ngành nước, ngành xăng dầu
SMS_CSKH_GROUP_5_TMDT	|	Customer Care	|	SMS_TMDT_CSKH	|	Group 5	|	Thương mại điện tử
SMS_CSKH_GROUP_6_INVOICE	|	Customer Care	|	SMS_INVOICE_CSKKH	|	Group 6	|	Hóa đơn điện tử/ Thanh toán điện tử/ Xác thực điện tử (qua QR code, mã vạch)
SMS_CSKH_GROUP_2.1_EDU	|	Customer Care	|	SMS_EDU_CKSH	|	Group 2.1	|	Lĩnh vực giáo dục
SMS_CSKH_GROUP_1_BHIEM	|	Customer Care	|	SMS_BHIEM_CSKH	|	Group 1	|	Lĩnh vực bảo hiểm
SMS_CSKH_GROUP_2.1_YTE	|	Customer Care	|	SMS_YTE_CSKH	|	Group 2.1	|	Lĩnh vực y tế


### 附录D 数据状态码说明

状态码		|说明  
:----	|:---
0		| 失效
1		| 有效

### 附录E 审核状态码说明

状态码		|说明  
:----	|:---
0		| 不通过
1		| 审核通过
2		| 审核中

### 附录F 发送状态码说明

类型 |状态码	|说明  
:---- |:----	|:---
DR |1		| Success
DR |2		| Failure
DR |3		| Processing
Retrieve |1		| Success
Retrieve |2		| Failure
Retrieve |3		| Processing

### 附录G 签名规则

- 调用方发起请求前，生成本次请求参数的键值对
- 按参数key(字典升序)排序
- 将参数集按"&key=value"格式拼接，另补充appKey 
- 最后通过MD5得出sign值

关键代码示例(java)：
``` JAVA
public static String createSign(Map<String, String> params, String appKey){
    StringBuilder sb = new StringBuilder();
    Map<String, String> sortParams = new TreeMap<>(params);
    for (Map.Entry<String, String> entry : sortParams.entrySet()) {
        String key = entry.getKey();
        if(StringUtils.isBlank(entry.getValue())){
            continue;
        }
        String value = entry.getValue().trim();
        if (StringUtils.isNotEmpty(value)){
            sb.append("&").append(key).append("=").append(value);
        }
    }
    String stringA = sb.toString().replaceFirst("&","");
    String stringSignTemp = stringA + appKey;
    String signValue = Md5Encrypt.md5(stringSignTemp);
    return signValue;
}

```
关键代码示例(js)：
``` JAVASCRIPT
function(rawData, appKey){
    var rawJSON = JSON.parse(rawData);
    delete rawJSON["sign"]

    rawJSON["timestamp"] = 'set timestamp';
    let arr=[];
    for(var key in rawJSON){
        arr.push(key)
    }
    arr.sort();

    let str='';
    for(var i in arr){
        if(!rawJSON[arr[i]]){
            continue;
        }
        str +=arr[i]+"="+rawJSON[arr[i]]+"&";
    }
    str = str.substr(0, str.length - 1) + appKey;
    //md5
    var sign = CryptoJS.MD5(str);
    rawJSON["sign"] = sign+"";
    return rawJSON;
}

```

### 附录H 特殊字符及注意事项(V2.3.2.brand)
1. 内容中禁止使用违反国家相关法律法规的词语。
2. 允许如下特殊字符 ? /n ! # $ % ( ) * + , - . / : ; = @ [ \ ] _ | } { ~ £ ¥ § ¿ ¤ Ä Å Æ Ç É Ñ Ø ø Ü ß Ö à ä å æ è é ì ñ ò ö ù ü Δ Φ Γ Λ Ω Π Ψ Σ Θ Ξ
3. 以下字符需要先行转义 &amp;(&amp;amp;) &gt;(&amp;gt;) &quot;(&amp;quot;) &#x27;(&amp;#x27;) &lt;(&amp;lt;)
