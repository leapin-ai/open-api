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
#### 4.2.2 获取Token
调用其他接口之前，先调用下本接口获取token。

##### 访问地址 
<pre><code>POST /access_token/
</code></pre>
##### 接口参数
|参数|类型|必填|说明|
|--|--|--|--|
|access_id|string|是|leapin access id，LeapIn saas平台获取|
|access_secret|string|是|leapin access secret，LeapIn saas平台获取|

##### 返回参数
|参数|类型|说明|
|--|--|--|
|code|int|状态码，0：成功，10001:缺少access_id，10002:缺少access_secret，10003:无效的access_id或access_secret|
|error_msg|string|错误信息|
|token|string|token|

### 4.3 请求参数类型说明
接口参数统一使用json格式传递
<pre><code>Content-Type: application/json
</code></pre>
### 4.4 验证与授权说明
首先调用“获取token接口”，可获取一个专属的token值，然后请求其它的每一个接口，都需要在header里面设置获取的token。

示例如下：
<pre><code>"Authorization" : "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJsZ..."
</code></pre>
注意：Bearer后有一个空格，后面的“eyJ0eXAiOiJKV1QiLC...”替换成获取的token
### 4.5 请求头示例

    {
"Content-Type": "application/json",
"x-leapin-open-api-access-id": "< leapin access id>",
"Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJsZ..."
    }

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
|create_time|创建时间|
|status|职位状态（0：开放，1：关闭）|


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
"create_time": "",
"status": 0
    },
    ...
]
    }
}</code></pre>


### 5.2 邀请候选人
- 接口
<pre><code>POST /jobs/< job_id >/applications/ </code></pre>

- 请求参数

|字段|类型|说明|必填|
|--|--|--|--|
|candidate_list|array|候选人数据列表|是|
|trigger_notice|bool|是否开启LeapIn平台发送通知，默认False不开启，True：开启|否|
|expire_days|int|AI面试链接过期天数，不传此参数默认7天|否|


- candidate_list参数说明

|字段|类型|说明|必填|
|--|--|--|--|
|unique_id|int|候选人在调用方系统中id|否|
|name|string|候选人姓名|是|
|email|string|候选人邮箱|是|
|mobile|string|候选人手机|是|
|mobile_country_code|string|号码区号，默认86|否|
|gender|int|候选人性别，0：男，1：女|否|
|avatar_url|string|候选人头像|否|
|resume_url|string|候选人简历|否|


- 返回值中data说明

|字段|类型|说明|
|--|--|--|
|id|int|候选人申请记录id|
|unique_id|int|候选人在调用方系统中id|
|interview_url|string|AI面试链接|
|status|int|候选人状态，0：已邀请，1：已提交AI面试，3：复试，4：入职，5：不合适|
|report_status|int|候选人报告状态， 0：不存在，1：生成中，2：生成失败，3：生成成功|
|invite_expire_time|string|测评过期时间|

- 返回示例

<pre><code>{
    "code": 0,
    "error_msg": "",
    "data": {
"id": 3847, 
"name": "测试", 
"email": "xxx@xxx",
"mobile": "xxxxxxxxxxx", 
"mobile_country_code": "86", 
"interview_url": "https://******", 
"unique_id": 123
"create_time": "",
"invite_expire_time": "2023-06-01 23:59:00",
"status": 0,
"report_status": 0
    }
}</code></pre>


### 5.3 候选人查询列表
- 接口
<pre><code>GET /jobs/applications/</code></pre>

- 查询参数

|字段|类型| 说明    |必填|
|--|--|-------|--|
|page|int| 分页第几页 |否|
|page_size|int| 分页数据条数 |否|
|status|int| 候选人状态 |否|
|job_id|int| 职位id  |否|
|report_status|int| 候选人报告状态 |否|


- 示例
<pre><code>https://app.leapin-ai.com/api/open/jobs/applications/?page_size=10&job_id=555&report_status=3</code></pre>

- 返回值中data说明

|字段|说明|
|--|--|
|count|返回条目总数|
|next|下一页请求链接|
|previous|上一页请求链接|
|results|返回当前页候选人列表，参考“候选人信息说明”|



- 候选人信息说明
 
 *\*注：以下参数因调用方式不同，部分字段不会返回，详情见说明，无特殊说明则为必返字段*


|字段| 含义| 注解 |说明|
|--|--|--|--|
|id| 候选人申请id  | ||
|name| 候选人姓名| ||
|email| 候选人邮箱| ||
|mobile| 候选人手机号码  | ||
|mobile_country_code| 号码区号 | ||
|status| 候选人状态| ||
|create_time| 候选人创建时间  | ||
|invite_time| 候选人被邀请时间 | ||
|submit_time| 候选人提交时间  | ||
|report_status| 候选人报告状态  | ||
|leapin_rank| 候选人排名| ||
|leapin_total_score| 候选人分数| ||
|leapin_report_url| 候选人报告链接  | ||
|invite_expire_time| 过期时间 | ||
|match_rate| 匹配度  | |目前仅用于租户平台调用，第三方平台调用不返回 |
|match_grade| 匹配等级 | |目前仅用于租户平台调用，第三方平台调用不返回 |
|rank_total| 参与排名的总数| |目前仅用于租户平台调用，第三方平台调用不返回 |
|cv_url| 简历链接| |目前仅用于租户平台调用，第三方平台调用不返回 |
|soft_ability_answer| 软能力问题回答数据 | |目前仅用于租户平台调用，第三方平台调用不返回 |
|hard_ability_answer| 硬能力问题回答数据 | |目前仅用于租户平台调用，第三方平台调用不返回 |
|hard_ability_assignment_problem| 硬能力赋值问题回答数据 |返回参数中若出现英语口语能力字段，该字段数据较特殊，待后续优化，详见返回示例 |目前仅用于租户平台调用，第三方平台调用不返回 |
|ability_analysis| 能力分析| advantage:优势，medium:中等，to_be_improved:待发展 |目前仅用于租户平台调用，第三方平台调用不返回 |
|big_five_score| 大五人格测评得分 | a:宜人性，c:尽责性，e:外向性，n:情绪性，o:开放性       |目前仅用于租户平台调用，第三方平台调用不返回 |

- 返回示例
<pre><code>

{
    "code": 0,
    "error_msg": "",
    "data": {
        "count": 3,
        "next": null,
        "previous": null,
        "results": [
            {
                "id": 17,
                "name": "xxxx",
                "email": "xxxx",
                "mobile": "xxxx",
                "mobile_country_code": "xxxx",
                "status": 0,
                "create_time": "2021-01-05 20:00:00",
                "submit_time": "2021-01-15 20:00:00",
                "invite_time": "2021-01-05 20:00:00",
                "report_status": 0,
                "leapin_total_score": 75.0,
                "leapin_rank": 3,
                "leapin_report_url": "https://xxxxxx",
                "invite_expire_time": "2023-06-01 23:59:00",
                "match_rate": 60,
                "match_grade": "较差",
                "rank_total": 10,
                "cv_url": "https://xxxxxx",
                "soft_ability_answer": [
                    {
                        "id": 12,
                        "question_id": 1,
                        "question_name": "你的优势是什么？",
                        "video_url": "https://xxxxxx",
                        "score": 80,
                        "is_cheat": False,
                        "score_description": "中等",
                        "competency_description": "",
                        "competency_name": "适应能力",
                        "competency_id": 876,
                    }
                ],
                "hard_ability_answer": [
                    {
                        "id": 12,
                        "question_id": 1,
                        "question_name": "你毕业的院校及所学专业是什么？",
                        "video_url": None,
                        "score": "",
                        "is_cheat": False,
                        "score_description": "",
                        "competency_description": "",
                        "competency_name": "毕业院校背景",
                    }
                ],
                "hard_ability_assignment_problem": [
                   {
                        "id": 8681,
                        "oral_id": 68,
                        "name":"英语口语能力",
                        "max_score": None,
                        "total": 10,"data": [],
                        "total_score": 3.1,
                        "is_success": True,
                        "is_checked": True,
                        "is_cheat": False,
                        "fluency": 3.0,
                        "rhythm": 5.0,
                        "relevance": None,
                        "integrity": 5.0,
                        "pronunciation": 6.0,
                        "grammatical_accuracy": None,
                        "vocabulary_richness": None,
                        "question_type": 4,
                        "question_category": 4,
                        "answer_type": 3,
                        "total_application_count": 2
                    },
                    {
                         "name":"学位","id": 8689,"total_application_count": 2,"question_type": 0,"total": 1,
                         "max_score": 1,
                         "total_score": 1,"answer_type": 0,
                         "data": [
                             {"name":"学士","value": 1,"index": 0,"option_score": 0},
                             {"name":"硕士","value": 1,"index": 1, "option_score": 1}],
                          "score": 0
                    }

                ],
                "ability_analysis": {"advantage": [],"medium": [],"to_be_improved": []},
                "big_five_score": None
            ...
        ]
            
    }
}
</code></pre>

## 6. 通用状态码

|字段|说明|
|--|--|
|0|成功|
|403|无效|
|10001|缺少access id|
|10002|缺少access secret|
|10003|无效的access id或access secret|
|40000|参数错误|
|50000|服务器内部错误|
