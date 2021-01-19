# LeapIn SaaS平台开放接口文档

## 1. 简介
LeapIn提供开放接口供第三方平台进行集成。包括ATS，HCM等系统。

## 2. 对接流程
<img src="https://github.com/leapin-ai/open-api/blob/main/LeapIn%20Integration.jpg"></img>

## 3. 概览
|API|描述|
|--|--|
|jobs|职位相关接口|
|applications|职位申请相关接口|

## 4. 接口调用
### 4.1 接口地址
<pre><code>生产环境：https://app.leapin-ai.com/api/open
测试环境：https://staging.app.leapin-ai.com/api/open</code></pre>

### 4.2 接口认证
#### 4.2.1 登录saas端
获取leapin access id和leapin access secret
#### 4.2.2 生成JWT
##### 构造JWT中的payload  

payload中的参数  

|Key|Value|
|--|--|
|leapin-access-id|API密钥ID，字符串|
|nonce|随机字符串，每次请求需要产生不同的随机数，防止重放攻击|

示例
<pre><code>{
    "nonce": "xxxxxxx",
    "leapin-access-id": "< leapin access id>"
}</code></pre>

##### 使用leapin access secret生成JWT token

|Key|Value|
|--|--|
|algorithm|HS256|

python (<a href=https://github.com/jpadilla/pyjwt/>pyjwt</a>)
<pre><code>import jwt
import uuid
payload['nonce'] = str(uuid.uuid4())
payload['leapin-access-id'] = '< leapin access id>'
jwt_token = jwt.encode(payload, '< leapin access secret >', algorithm='HS256')</code></pre>

java
<pre><code>try {
    Algorithm algorithm = Algorithm.HMAC256("< leapin access secret >");
    String jwtToken = JWT.create()
        .withClaim("nonce", UUID.randomUUID().toString())
        .withClaim("leapin-access-id", "< leapin access secret >")
        .sign(algorithm);
} catch (JWTCreationException exception){
    //Invalid Signing configuration / Couldn't convert Claims.
}</code></pre>


### 4.3 调用
- HTTP请求头

|Key|Value|
|--|--|
|x-leapin-open-api-access-id|API密钥ID|
|x-leapin-open-api-token|JWT token|

### 4.4 接口返回格式
- HTTP返回头

|Key|Value|
|--|--|
|x-leapin-open-api-request-id|请求在leapin平台中的ID|

- 返回数据使用json格式

|字段|说明|
|--|--|
|code|API返回码，详见各接口说明|
|data|API返回数据，详见各接口说明|
|error_msg|API返回错误消息，详见各接口说明|

## 5. 接口说明
### 5.1 职位列表
- 接口
<pre><code>GET /jobs/</code></pre>

- 查询参数

|字段|说明|
|--|--|
|page|分页第几页|
|page_size|分页数据条数|

- 返回值中data说明

|字段|说明|
|--|--|
|count|返回条目总数|
|next|下一页请求链接|
|previous|上一页请求链接|
|results|返回当前页职位列表，参考“职位信息说明”|

- 职位信息说明

|字段|说明|
|--|--|
|id|职位id|
|name|职位名称|
|wechat_qr_link|职位微信小程序二维码|
|app_qr_link|职位LeapIn APP二维码|
|invite_code|职位邀请码|
|status|职位状态|

- 返回示例
<pre><code>{
    "code": 0,
    "error_msg": "",
    "data": {
        "count": 3,
        "next": null,
        "previous": null,
        "results": [
            {
                "id": 17,
                "name": "python开发工程师",
                "wechat_qr_link": "xxxx",
                "app_qr_link": "xxxx",
                "invite_code": "xxxx",
                "create_date": "",
                "status": 0
            },
            ...
        ]
    }
}</code></pre>


### 5.2 邀请候选人
- 接口
<pre><code>POST /jobs/< job_id >/applications/</code></pre>

- 请求参数

|字段|说明|必填|
|--|--|--|
|name|候选人姓名|是|
|unique_id|候选人在调用方系统中id|否|
|email|候选人邮箱|与mobile二选一|
|mobile|候选人手机|与email二选一|
|gender|候选人性别|否|
|avatar_url|候选人头像|否|
|resume_url|候选人简历|否|

- 返回值中data说明

|字段|说明|
|--|--|
|id|候选人申请id|
|wechat_qr_link|邀请候选人微信小程序二维码|
|app_qr_link|邀请候选人LeapIn APP二维码|
|invite_code|候选人邀请码|
|status|候选人状态|
|report_status|候选人报告状态|


- 返回示例

<pre><code>{
    "code": 0,
    "error_msg": "",
    "data": {
        "id": 17,
        "wechat_qr_link": "xxxx",
        "app_qr_link": "xxxx",
        "invite_code": "xxxx",
        "create_date": "",
        "status": 0,
        "report_status": 0
    }
}</code></pre>


### 5.1 候选人查询列表
- 接口
<pre><code>GET /jobs/< job_id >/applications/</code></pre>

- 查询参数

|字段|说明|
|--|--|
|page|分页第几页|
|page_size|分页数据条数|
|status|候选人状态|
|report_status|候选人报告状态|

- 返回值中data说明

|字段|说明|
|--|--|
|count|返回条目总数|
|next|下一页请求链接|
|previous|上一页请求链接|
|results|返回当前页候选人列表，参考“候选人信息说明”|

- 职位信息说明

|字段|说明|
|--|--|
|id|候选人申请id|
|wechat_qr_link|邀请候选人微信小程序二维码|
|app_qr_link|邀请候选人LeapIn APP二维码|
|invite_code|候选人邀请码|
|status|候选人状态|
|invite_date|候选人邀请时间|
|submit_date|候选人提交时间|
|report_status|候选人报告状态|
|leapin_score|候选人分数|
|leapin_rank|候选人排名|
|leapin_competency_score|候选人胜任力分数|
|leapin_big5_score|候选人大五人格分数|
|leapin_report_url|候选人报告链接|

- 返回示例
<pre><code>{
    "code": 0,
    "error_msg": "",
    "data": {
        "count": 3,
        "next": null,
        "previous": null,
        "results": [
            {
                "id": 17,
                "wechat_qr_link": "xxxx",
                "app_qr_link": "xxxx",
                "invite_code": "xxxx",
                "create_date": "",
                "status": 0,
                "invite_date": "2021-01-05 20:00:00",
                "submit_date": "2021-02-05 20:00:00",
                "report_status": 0,
                "leapin_score": 75.0,
                "leapin_rank": 3,
                "leapin_competency_score": [
                    {
                        "name": "沟通能力",
                        "score": 69
                    },
                    {
                        "name": "领导力",
                        "score": 78
                    },
                    ...
                ],
                "leapin_big5_score": {
                    "a": 50,
                    "c": 50,
                    "e": 50,
                    "n": 50,
                    "o": 50
                },
                "leapin_report_url": "https://xxxxxx"
            },
            ...
        ]
    }
}</code></pre>
