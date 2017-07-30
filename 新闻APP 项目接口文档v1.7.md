# 新闻 APP 项目接口文档v1.7

[TOC]

 ### 1.更新记录

#### 1.1.第一次更新（2017/7/28）

> **3.3忘记密码操作的修改：**
>
> ​	1.连接点击通过验证修改成验证码正误判断。
>
> ​	2.设置新密码时同验证码一起提交判断。
>
> **4.1.新闻主页面浏览的修改：**
>
> ​	1.将请求时无提交参数改成请求时有参数提交。提交参数为新闻开始展示条数。
>
> **5.2.将封号状态从未审批改成已封号**
>
> **5.3.发布新闻项的修改：**
>
> ​	1.将发布新闻时需要提交的参数详细化
>
> **5.7.审批管理员的修改**
>
> ​	1.增加5.7.3的拒绝审批操作。发送邮件通知申请失败的同时，在数据库中删除该账户数据。
>
> **5.8.将所有的提交方式改为POST**
>
> **5.9.将 id 值改为 int 类型值**

#### 1.2.第二次更新（2017/7/28）

> 5.3.发布新闻项的键值对的键字段改变
>
> 将所有路径中的 manager 改为 admin
>
> 将所有新闻封面字段名全部改成 newsFace

#### 1.3.第三次更新（2017/7/29）

> 状态码（StatusCode）去掉4、5、7

#### 1.4.第四次更新（2017/7/29）

> 4.2.改为附件下载请求，移动端下载文件之后需要发送一个请求给服务器
>
> 5.2.url 链接从 deleteaccount 改为 restrictaccount、传输数据改为"managerId":"管理员id"。防止歧义
>
> 删掉所有具体状态信息，即(statusInfo)，状态信息直接靠状态 id 提示
>
> 更新了 news 模型类，增加 managerId，删除了 managerName
>
> 5.3.增加"managerId":"新闻撰写人id"键值对
>
> 5.5.查看附件下载情况部分加戏：
>
> ​	1.展示附件下载次数添加普通管理员可查看的权限，但只能查看自己的，超级管理员可查看全部。
>
> ​        2.展示的附件名上还有连接，可以点击查看附件被下载的记录
>
> 5.6.查看管理员列表处加上封号和查看该管理员新闻的操作
>
> 5.8.删除附件下载次数键值对，放到附件显示列表那里显示
>
> 
>
> 数据库增加两个表：游客表、附件下载记录表

#### 1.5.第五次更新（2017/7/29）

>修改了登录功能的返回参数，只返回状态码和一个管理员实体类。
>
>增加了3.3和3.4两个获取新闻的操作，根据正常登录后返回的实体类判断是否为超级管理员，进而发送相应的请求。

#### 1.6.第六次更新（2017/7/30）

> 忘记了一个重要接口！增加了4.2.的新闻主体内容请求接口。
>
> 去掉了昨晚增加的3.3和3.4两个接口。统一返回同游客一样的新闻概述。管理者要进行进一步操作时再去点击按钮进行操作。

#### 1.7.第七次更新（2017/7/30)

> 5.1.超级管理员添加管理员 url 与管理员注册 url 重复，修改为 addmanager ，避免歧义。

### 2.数据格式统一标准

- **后台返回统一格式：**

  ```json
  {
    "state": "状态码",
    "data":"返回数据; 若非查询类操作, 则返回null;"
  }
  ```

- **状态码定义**：

  - 1 : 一切正常
  - 5000 : 后台服务器发生未知错误
  - 2 : 用户名（邮箱）已经存在
  - 3 : 用户名（邮箱）不存在
  - 6 : 邮箱格式不正确
  - 8 : 密码错误
  - 9 : 账户未激活
  - 10 : 账户未审批
  - 11 : 账户被封
  - 12 : 新闻标题为空
  - 13 : 新闻主题内容为空
  - 14 : 新闻作者为空
  - 15 : 新闻封面为空
  - 16 : 验证码错误

- **移动端传输json数据：**

  - 部分字段是不可缺少的（ 倘若缺少，则会提交失败 ），如管理员账户、密码
  - 未做说明的字段是可有可无的

### 3.注册登录模块

#### 3.1注册功能

##### 3.1.1 信息提交

- 请求 URL：http://ip:80/admin/addaccount
- 请求方式：POST
- 参数提交方式：application/json
- 备注：游客操作，账户为邮箱。服务器接收到信息之后存入数据库，但是账户状态字段为**未激活**，即，**仍无法登录**。
- 请求参数：

```json
{
  "managerAccount":"账户账号",//邮箱格式
  "managerPassword":"账号密码",//长度为6~20
  "managerName":"管理者姓名"//长度30以下
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

##### 3.1.2 邮箱验证

- 请求 URL：http://ip:80/admin/verifyaccount
- 请求方式：GET
- 参数提交方式：application/x-www-form-urlencoded
- 备注：登录邮箱，点击链接验证，发送一个请求，验证通过则将账户状态设置为**未审批。仍无法登录**。  **需要经过超级管理员验证之后，将审批状态设置为已审批状态，才能登录**
- 请求参数：

```json
{
  "managerAccount":"账户账号"
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

#### 3.2登录功能

- 请求 URL：http://ip:80/admin/login
- 请求方式：POST
- 参数提交方式：application/json
- 备注：如果为正常登陆，则进入APP的主页。如果是未激活状态则返回错误信息，并提醒用户去邮箱激活，如果激活链接已经过期，就让用户重新发送激活的请求。如果是未审批状态，则提醒管理员的注册请求还未经过审批。
- 请求参数：

```json
{
  "managerAccount":"账户账号",//邮箱格式
  "managerPassword":"账号密码"//长度为6~20
}
```

- 返回参数：

```json
{
  "state":"状态码",
  //若账户状态正常，则下面数据一并返回
   "data": 						// 管理员实体类，要判断是不是超级管理员
    {
      "managerId" : "管理员id",  // int 值类型
      "managerAccount" : "账户账号",
      "managerPassword" : "账户密码,
      "managerSuper" : "int类型", // 1为超级管理员，0为普通管理员
      "managerStatus" : "管理员的状态"
      "newsList" : "管理员对应的新闻"
    }
}
```

#### 3.3 忘记密码功能

##### 3.3.1提交发送验证码请求

- 请求 URL：http://ip:80/admin/sendverifycode
- 请求方式：POST
- 参数提交方式：application/json
- 备注：请求服务器发送验证码到账户邮箱
- 请求参数：

```json
{
  "managerAccount":"账户账号"
}
```

- 返回参数：

```json
{
  "state":"状态码",
  "stateInfo":"具体状态信息" // 判断账户是否存在与数据库中，若不存在则返回提示消息，不发送验证码。若存在则提示去邮箱查收
}
```

##### 3.3.2设置新密码

- 请求 URL：http://ip:80/admin/setnewpassword
- 请求方式：POST
- 参数提交方式：application/json
- 备注：若密码正确则返回数据，否则返回信息提示
- 请求参数：

```json
{
  "managerAccount":"账户账号",
  "verifyCode":"修改密码的验证码",
  "managerPassword":"账号新密码" // 长度为6~20
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

### 4.游客浏览模块

#### 4.1.新闻主页面浏览

- 请求 URL：http://ip:80/reader/read
- 请求方式：POST
- 参数提交方式：application/json
- 备注：从主页点击游客链接的请求，因此无需账户等信息。直接返回新闻标题等数据。**但，需要提交请求起始条数。（从第几条开始展示。如：10。则返回第10到第20条新闻）**
- 请求参数：


```json
{
  "startNews":"新闻起始条数"
}
```

- 返回参数：

```json
{
  "state":"状态码",
  "data": [						//新闻的数组：每次 10 条
    {
      "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsAuthor": "新闻撰写人",
      "newsTime":"新闻上传时间"
    },
    {
     "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsAuthor": "新闻撰写人",
      "newsTime":"新闻上传时间"
    },
    {
      "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsAuthor": "新闻撰写人",
      "newsTime":"新闻上传时间"
    }
     {
      "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsAuthor": "新闻撰写人",
      "newsTime":"新闻上传时间"
    }
     {
      "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsAuthor": "新闻撰写人",
      "newsTime":"新闻上传时间"
    }
  ]
}
```

#### 4.2.新闻详细内容浏览

- 请求 URL：http://ip:80/reader/newsdetail
- 请求方式：POST
- 参数提交方式：application/json
- 备注：请求所点击新闻的详细内容，服务器将新闻主体和附件链接等东西一并返回给移动端显示
- 请求参数：

```json
{
  "newsId": "新闻id",  // int 值类型 
}
```

- 返回参数：

```json
{
  "state":"状态码",
  "data":					
    {
      "newsTitle": "新闻标题",
      "newsBody":"新闻主体内容",
      "newsAuthor": "新闻撰写人",
      //以下用集合的数据展示
      "newsViceFile":"新闻附件路径集合"
    }
}
```

#### 4.3.附件下载通知请求

- 请求 URL：http://ip:80/reader/downloadfile
- 请求方式：POST
- 参数提交方式：application/json
- 备注：附件下载请求连接放于文章下，点击即可将下载。下载的同时发送该请求给服务器，告知下载。服务器对应的 Servlet 操作 vicefile 数据库中对应 filesPath 的 downloadtime 字段+1
- 请求参数：

```json
{
  "filesPath":"附件路径"
}
```

- 返回参数：无

### 5.管理者模块

#### 5.1 添加管理员（类似注册操作，但，直接将状态字段设置成正常！）

- 请求 URL：http://ip:80/admin/addmanager
- 请求方式：POST
- 参数提交方式：application/json
- 备注：超级管理员操作，账户状态为已验证
- 请求参数：

```json
{
  "managerAccount":"账户账号",
  "managerPassword":"账号密码",
  "managerName":"管理者姓名"
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

#### 5.2 对管理员封号

- 请求 URL：http://ip:80/admin/restrictaccount
- 请求方式：POST
- 参数提交方式：application/json
- 备注：将状态字段设置为已封号
- 请求参数：

```json
{
  "managerId":"管理员id"
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

#### 5.3 发布新闻

- 请求 URL：http://ip:80/admin/addnews
- 请求方式：POST
- 参数提交方式：multiply/form-data
- 备注：可以以表单的提交方式传输，又有新闻内容，又有封面图片
- 请求参数：

```json
{
  "newsTitle":"新闻标题",
  "managerId":"新闻撰写人id"
  "newsBody":"新闻主体内容"
  "newsAuthor": "新闻撰写人" // 该项为管理者姓名，请求时一并发送
  // 下面为数据流提交
  "newsFace": // 相当与上传图片操作,
  "filesUUID": // 附件以流的形式提交
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

#### 5.4 删除新闻

- 请求 URL：http://ip:80/admin/deletenews
- 请求方式：POST
- 参数提交方式：application/json
- 备注：无
- 请求参数：

```json
{
  "newsId":"新闻id"  // int 值类型 
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

#### 5.5 统计全部附件下载次数

##### 5.5.1.展示该管理员所具有的附件下载次数

- 请求 URL：http://ip:80/admin/downloadtime
- 请求方式：POST
- 参数提交方式：application/json
- 备注：根据管理员 id 判断是普通管理员还是超级管理员。普通管理员只能查看自己的附件，超级管理员能查看全部的附件。
- 请求参数：


```json
{
  "managerId":"管理员id"
}
```

- 返回参数：

```json
{
  "state":"状态码",
  "data": [						//附件数组
    {
      "filesId": "附件id",  // int 值类型 
      "filesName": "附件名称",
      "downloadTime":"附件被下载次数"
    },
    {
      "filesId": "附件id",  // int 值类型 
      "filesName": "附件名称",
      "downloadTime":"附件被下载次数"
    },
    {
      "filesId": "附件id",  // int 值类型 
      "filesName": "附件名称",
      "downloadTime":"附件被下载次数"
    }
  ]
}
```

##### 5.5.2.展示该附件下载记录

- 请求 URL：http://ip:80/admin/downloaddetial
- 请求方式：POST
- 参数提交方式：application/json
- 备注：根据附件 id 进入附件下载记录表查看下载该附件的人员。
- 请求参数：

```json
{
  "filesId":"附件id"
}
```

- 返回参数：

```json
{
  "state":"状态码",
  "data": [						//下载该附件的人员
    	"downloadName","downloadName","downloadName"
  		]
}
```

#### 5.6.浏览管理员列表

- 请求 URL：http://ip:80/admin/showmanager
- 请求方式：POST
- 参数提交方式：application/json
- 备注：只有超级管理员有该权限，每个管理员后跟着封号链接。每个管理员点击之后即可进入他已编辑的新闻页面。相当于5.8操作
- 请求参数：无


- 返回参数：

```json
{
   "state":"状态码",
   "data": [						//管理员数组
    {
      "managerId": "管理员id",  // int 值类型
      "managerAccount": "管理员账户",
      "managerName": "管理员名称",
      "managerStatus":"管理员状态"
    },
    {
      "managerId": "管理员id",  // int 值类型
      "managerAccount": "管理员账户",
      "managerName": "管理员名称",
      "managerStatus":"管理员状态"
    },
    {
       "managerId": "管理员id",  // int 值类型
      "managerAccount": "管理员账户",
      "managerName": "管理员名称",
      "managerStatus":"管理员状态"
    }
  ]
}
```

#### 5.7.审批管理员

##### 5.7.1.展示未审批管理员列表

- 请求 URL：http://ip:80/admin/showmanagerapproval
- 请求方式：POST
- 参数提交方式：application/json
- 备注：只有超级管理员有该权限
- 请求参数：无


- 返回参数：

```json
{
   "state":"状态码",
   "data": [						//待审批管理员数组
    {
      "managerId": "管理员id",  // int 值类型 
      "managerAccount": "管理员账户",
      "managerPassword": "管理员密码",
      "managerName": "管理员名称"
    },
    {
      "managerId": "管理员id",  // int 值类型 
      "managerAccount": "管理员账户",
      "managerPassword": "管理员密码",
      "managerName": "管理员名称"
    },
    {
      "managerId": "管理员id",  // int 值类型 
      "managerAccount": "管理员账户",
      "managerPassword": "管理员密码",
      "managerName": "管理员名称"
    }
  ]
}
```

##### 5.7.2.通过管理员审批

- 请求 URL：http://ip:80/admin/managerapproval
- 请求方式：POST
- 参数提交方式：application/json
- 备注：只有超级管理员有该权限
- 请求参数：

```json
{
  "managerId": "管理员id"
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

##### 5.7.3.拒绝审批申请

- 请求 URL：http://ip:80/admin/approvalrefuse
- 请求方式：POST
- 参数提交方式：application/json
- 备注：拒绝操作，**服务器会发送一通知邮件给该账户，通知审批被拒绝，并在数据库中将该账户删除**。
- 请求参数：

```json
{
  "managerId": "管理员id"
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

#### 5.8.管理员查看自己的新闻

- 请求 URL：http://ip:80/admin/showownnews
- 请求方式：POST
- 参数提交方式：application/json
- 备注：管理员操作。显示文章的同时，每篇文章的附件下载次数也紧跟其后
- 请求参数：

```json
{
  "managerId": "管理员id"
}
```

- 返回参数：

```json
{
  "state":"状态码",
  "data": [						//新闻的数组
    {
      "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsTime":"新闻上传时间"
    },
    {
     "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsTime":"新闻上传时间"
    },
    {
      "newsId": "新闻id",  // int 值类型 
      "newsTitle": "新闻标题",
      "newsFace": "新闻封面图片路径",
      "newsTime":"新闻上传时间"
    }
  ]
}
```

#### 5.9.注销

- 请求 URL：http://ip:80/admin/logout
- 请求方式：POST
- 参数提交方式：application/json
- 备注：管理员操作
- 请求参数：

```json
{
  "managerId": "管理员id"  // int 值类型 
}
```

- 返回参数：

```json
{
  "state":"状态码"
}
```

