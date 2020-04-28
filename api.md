#  BTYUN官方API文档

#### BTYUN交易所开发文档
- [介绍](#Introduction)
- [API接口加密验证](#a1)
    - [生成API Key](#a2)
    - [签名](#a4)
    - [请求交互](#a6)
        - [请求](#a7)
    - [标准规范](#a9)
        - [时间戳](#b1)
        - [例子](#b2)
        - [数字](#b3)
        - [限流 - REST API](#b4)
- [业务API参考](#b5)
  - [open-api](#b6)
    -   [获取所有的交易对](#1)
    -   [获取最新成交记录](#2)
    -   [获取实时行情](#3)
    -   [获取市场深度](#4)
    -   [获取K线信息](#5)
    -   [获取个人资产](#6)
    -   [创建订单](#7)
    -   [取消委托单](#8)
    -   [获取当前委托](#9)
    -   [获取订单详情](#10)
    -   [获取成交记录](#11)

---


# <span id="Introduction">介绍</span>

欢迎使用交易所开发者文档

本文档提供了相关API的使用方法介绍。open-api包含了资产余额，获取全部委托，获取全部成交记录等接口。

---

# <span id="a1">API接口加密验证</span>

## <span id="a2">生成API Key</span>
在对任何请求进行签名之前，您必须通过 [交易所]【用户中心】-【API】创建一个API key。 创建key后，您将获得2个必须记住的信息：

- API Key
 
- Secret Key

API Key 和 Secret Key将由随机生成和提供

## <span id="a4">签名</span>

生成待签名的字符串
    
1、先将参数以其参数名的字典序升序进行排序（首先比较所有参数名的第一个字母，按abcd顺序排列，若遇到相同首字母,则看第二个字母, 以此类推。）

2、遍历排序后的字典，将所有参数按"keyvalue"格式拼接在一起（非空参数）

3、使用MD5对待签名串求签

例：

api_key = 1234567

time = 12312312312137

secret_key = 789654

sign=md5(api_key1234567time12312312312137789654secret_key789654)，32位大写

4、secret_key不参与加密


## <span id="a6">请求交互</span>

REST访问的根URL：``` https://www.btyun.me/open/api.html?action=xx ```
注：action为每个接口的名称，也不参与加密

###  <span id="a7">请求</span>
所有请求基于Https协议，请求头信息中Content-Type 需要统一设置为:'application/x-www-form-urlencoded’。

#### 状态码

状态码    | 说明               | 备注
------ | ---------------- | ---------------------
200      | 成功               | code=200 成功， code <0 失败
-10001  | 网络错误，请稍后重试           |
-10002  | 访问频率过快，访问频率           |
-10003  | 时间戳已失效           |
-10004  | API未审核通过，请联系网站客服           |
-10005  | 无交易权限           |
-10006  | 无提现权限           |
-10007  | IP无权限           |
-10008  | api_key错误           |
-10009  | 参数错误           |
-100010  | 签名错误           |
-100011  | API不存在           |

**请求交互说明**

1、请求参数：根据接口请求参数规定进行参数封装。

2、提交请求参数：将封装好的请求参数通过POST/GET/DELETE等方式提交至服务器。

3、服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。

4、数据处理：对服务器响应数据进行处理。

**成功**

HTTP状态码200表示成功响应，并可能包含内容。如果响应含有内容，则将显示在相应的返回内容里面。

**常见错误码**

- 400 Bad Request – Invalid request forma 请求格式无效

- 401 Unauthorized – Invalid API Key 无效的API Key

- 403 Forbidden – You do not have access to the requested resource 请求无权限

- 404 Not Found 没有找到请求

- 429 Too Many Requests 请求太频繁被系统限流

- 500 Internal Server Error – We had a problem with our server 服务器内部错误

如果失败，response body 带有错误描述信息

## <span id="a9">标准规范</span>

### <span id="b1">时间戳</span> 
除非另外指定，API中的所有时间戳均以微秒为单位返回。

请求签名中的ACCESS-TIMESTAMP的单位是秒，允许用小数表示更精确的时间。请求的时间戳必须在API服务时间的30秒内，否则请求将被视为过期并被拒绝。如果本地服务器时间和API服务器时间之间存在较大的偏差，那么我们建议您使用通过查询API服务器时间来更新http header。

###  <span id="b2">例子</span> 
1524801032573

###  <span id="b3">数字</span> 
为了保持跨平台时精度的完整性，十进制数字作为字符串返回。建议您在发起请求时也将数字转换为字符串以避免截断和精度错误。

整数（如交易编号和顺序）不加引号。

###  <span id="b4">限流</span>  
如果请求过于频繁系统将自动限制请求。

REST API

- 接口：我们通过IP限制公共接口的调用：每1秒最多10个请求。

---

# <span id="b5">业务API参考</span>

##  <span id="b6">open-api</span>


### <span id="1">获取所有的交易对</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=ex_mappings
2. 接口说明: (get请求)获取所有的交易对

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  200|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"获取所有的交易对",
    "time":1587977106463,
    "data":[
        {
            "symbol":12,//交易对ID
            "count1":4,//数量小数位
            "count2":4,//金额小数位
            "mapping":"YO_USDT"//交易对别名
        }
        ...
    ]
}
```
---

### <span id="2">获取最新成交记录</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=ex_transactions
2. 接口说明: (get请求)获取全站最新交易成交记录

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|symbol|  必填| 交易对ID|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  200|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"获取最新成交记录",
    "time":1587978455162,
    "data":[
        {
            "id":1,//序号
            "time":"17:07:30",//成交时间
            "price":"195.0100",//成交价格
            "amount":"0.7655",//成交数量
            "en_type":"bid",//成交类型
            "type":"买入"
        }
        ...
    ]
}
```
---

### <span id="3">获取实时行情</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=ex_market
2. 接口说明: (get请求)获取对应交易对的价格和成交信息

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|symbol|  必填| 交易对ID|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  200|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"获取实时行情",
    "time":1587978589615,
    "data":{
        "base_coin":"￥",//法币符号
        "base_coin_name":"CNY",
        "rmb_new":7.183,//汇率
        "last":171.28,//最新价
        "high":0,//24小时最高
        "low":0,//24小时最低
        "vol":0,//总成交数量
        "buy":175,//买一价
        "sell":183.73//卖一价
    }
}
```
---

### <span id="4">获取市场深度</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=order_depth
2. 接口说明: (get请求)获取对应交易对的市场深度

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|symbol|  必填| 交易对ID|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  200|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"获取市场深度",
    "time":1587978826659,
    "data":{
        "bids":[//买单
            [
                "179.0909",//价格
                "0.3383"//数量
            ]
            ...
        ],
        "asks":[//卖单
            [
                "215.4333",//价格
                "0.4889"//数量
            ]
            ...
        ]
    }
}
```
---

### <span id="5">获取K线信息</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=ex_kline
2. 接口说明: (get请求)获取对应交易对的市场深度

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|symbol|  必填| 交易对ID|
|step|  必填| 时间区间（秒）,目前支持（1分钟，3分钟，5分钟，15分钟，30分钟，1小时，2小时，4小时，6小时，12小时，24小时，3天，7天），转换成对应的秒数|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  200|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"获取K线数据",
    "time":1587979272679,
    "data":[
        [
            1587964800000,//时间戳
            171.28,//开
            171.28,//高
            171.28,//低
            171.28,//收
            0//成交量
        ]
        ...
    ]
}
```
---

### <span id="6">获取用户资产</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=get_userinfo
2. 接口说明: (get请求)获取用户的资产详情

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  200|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"获取用户信息",
    "time":1587980479012,
    "data":{
        "free":{//可用金额
            "BTC":3.3,
            "ETH":34.4
            ...
        },
        "frozen":{//冻结金额
            "BTC":4.4,
            "ETH":3.44
            ...
        }
    }
}
```

---

### <span id="7">创建订单</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=create_orders
2. 接口说明: (get请求)创建委托单

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|symbol|  必填| 交易对ID|
|type|  必填| 委托单类型，0是买入，1是卖出|
|price|  必填| 委托价格|
|amount|  必填| 委托数量|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  0|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":0,
    "msg":"下单成功",
    "orderId":1610383//订单ID
}
```

---

### <span id="8">取消委托单</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=cancel_order
2. 接口说明: (get请求)创建委托单

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|order_id|  必填| 委托单ID|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  0|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "time":1588039595164,
    "code":200,
    "msg":"撤单成功"
}
```

---

### <span id="9">获取当前委托</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=get_open_orders
2. 接口说明: (get请求)根据交易对ID，获取未完成的委托单

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|symbol|  必填| 交易对ID|
|currentPage|  必填| 当前页，第一次调用传1|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  0|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"查询当前委单",
    "time":1588039859811,
    "totalPage":285,//总页数
    "currentPage":1,//当前页
    "data":[
        {
            "price":"175.0000",//委托价格
            "count":"0.9889",//委托数量
            "success_count":"0.1281",//已成交数量
            "amount":"173.0575",//委托金额
            "success_amount":"21.9580",//已成交金额
            "type":0,//类型
            "type_s":"买入",
            "status":2,//状态
            "status_s":"部分成交",
            "id":1501018,//订单ID
            "create_time":"2020-04-22 18:21:38"//委托时间
        }
        ...
    ]
}
```

---

### <span id="10">获取订单详情</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=get_order_details
2. 接口说明: (get请求)根据订单ID，获取订单的详情

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|   必填| api_key|
|time|  必填| 时间戳|
|order_id|  必填| 订单ID|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  0|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"根本id查委托订单详细信息",
    "time":1588040299427,
    "data":[
        {
            "price":"161.1523",//委托单价
            "count":"0.3296",//委托数量
            "success_count":"0.0000",//已成交数量
            "amount":"53.1157",//总金额
            "success_amount":"0.0000",//已成交金额
            "type":0,//类型
            "type_s":"买入",
            "status":1,//状态
            "status_s":"未成交",
            "id":1610313,//订单ID
            "create_time":"2020-04-23 09:32:22"//委托时间
        }
    ]
}
```
---

### <span id="11">获取成交委单记录</span>

1. 接口地址: https://www.btyun.me/open/api.html?action=get_order_history
2. 接口说明: (get请求)获取最近有成交的委托记录

|参数|    填写类型|   说明|
|--------|--------|--------|
|api_key|  必填| api_key|
|time|  必填| 时间戳|
|symbol|  必填| 交易对ID|
|currentPage|  必填| 当前页，第一次调用传1|
|sign|  必填| 签名|

返回值:

|字段|    实例| 解释|
|--------|--------|--------|
|code|  0|   |
|msg|   "success"|  code<0失败|
|data|如下|
```
{
    "code":200,
    "msg":"查询成交委单记录",
    "time":1588040848145,
    "totalPage":267,
    "currentPage":1,
    "data":[
        {
            "price":"171.2800",//委托单价
            "count":"0.8921",//委托数量
            "success_count":"0.8921",//已成交数量
            "amount":"152.7988",//委托金额
            "success_amount":"152.7988",//已成交金额
            "type":1, //类型
            "type_s":"卖出",
            "status":3, //状态
            "status_s":"完全成交",
            "id":1467488, //订单ID
            "create_time":"2020-04-22 12:55:00" //委托时间
        }
        ...
    ]
}
```
---
