﻿# HTTP

## 安装包

```shell 终端
dotnet add package Masa.Contrib.StackSdks.Scheduler
```

## 手动创建

![填写调度信息](https://cdn.masastack.com/stack/doc/scheduler/rc1/scheduler_http_insert.png)

![填写调度信息2](https://cdn.masastack.com/stack/doc/scheduler/rc1/scheduler_http_insert_2.png)

| 类型     | 描述                                                                                                                                  |
|----------|---------------------------------------------------------------------------------------------------------------------------------------|
| 请求类型 | `HTTP` 请求类型（`GET`，`POST`，`PUT`，`DELETE`，`HEAD`）                                                                               |
| 请求地址 | 调度请求的接口地址                                                                                                                    |
| 请求参数 | 接口参数                                                                                                                              |
| 校验条件 | **默认响应码 200**：接口 `HTTP` 响应码返回是否为 `200` <br/> **内容包含**：与**校验内容**配合使用<br/> **内容不包含**：与**校验内容**配合使用 |

## API创建
1. 注册 `Scheduler` 服务

   ```csharp Program.cs
   builder.Services.AddSchedulerClient("schedulers 服务地址");
   ```

2. 注册一个 `Job` 应用示例

   ```csharp
   using Masa.BuildingBlocks.StackSdks.Scheduler;
   using Masa.BuildingBlocks.StackSdks.Scheduler.Enum;
   using Masa.BuildingBlocks.StackSdks.Scheduler.Model;
   using Masa.BuildingBlocks.StackSdks.Scheduler.Request;
   using Microsoft.AspNetCore.Mvc;
   
   /// <summary>
   /// 一个测试的任务调度的Controller
   /// </summary>
   [ApiController]
   [Route("[controller]/[action]")]
   public class SchedulerHttpController : ControllerBase
   {
       private readonly ISchedulerClient _schedulerClient;
   
       public SchedulerHttpController(ISchedulerClient schedulerClient)
       {
           _schedulerClient = schedulerClient;
       }
   
       [HttpPost]
       public async Task<JobRegisterResult> Register()
       {
           var request = new AddSchedulerJobRequest
           {
               ProjectIdentity = "",
               Name = "",
               JobType = JobTypes.Http,
               CronExpression = "",
               OperatorId = Guid.Empty,
               HttpConfig = new SchedulerJobHttpConfig
               {
                   HttpMethod = HttpMethods.GET,
                   RequestUrl = "",
                   HttpParameters = new List<KeyValuePair<string, string>>(),
                   HttpHeaders = new List<KeyValuePair<string, string>>(),
                   HttpBody = "",
                   HttpVerifyType = HttpVerifyTypes.StatusCode200,
                   VerifyContent = ""
               }
           };
           var jobID = await _schedulerClient.SchedulerJobService.AddAsync(request);
           return new JobRegisterResult(jobID);
       }
   } 
   
   public record JobRegisterResult(Guid JobID);
   ```

   | 属性                | 描述                                      |
   |---------------------|-------------------------------------------|
   | **ProjectIdentity** | [项目](stack/pm/introduce) `ID`           |
   | **Name**            | `Job` 的名称                              |
   | **JobType**         | `Job` 的类型（`JobTypes.HTTP` 为 `HTTP`） |
   | **CronExpression**  | `Cron` 表达式（`Job` 执行的周期）         |
   | **OperatorId**      | 操作人/创建人                             |
   | **HttpMethod**      | 请求类型                                  |
   | **RequestUrl**      | 请求地址                                  |
   | **HttpParameters**  | 接口参数（`Query`）                       |
   | **HttpBody**        | 接口参数 (`Content`)                      |
   | **HttpVerifyType**  | 校验条件                                  |
   | **VerifyContent**   | 校验内容                                  |
