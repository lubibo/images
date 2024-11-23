- 已有架构逻辑
```
digraph G {
  rankdir=LR;

  upload_api -> vod_preupload;
  upload_api -> img_preupload;
  upload_api -> vod_reportupload;
  upload_api -> img_notify;
  
  vod_preupload -> GenerateImgId;
  vod_preupload -> Strategyservice[style="dashed,filled"];
  vod_preupload -> mediamanager[label="GenerateVID"];
  vod_preupload -> mediamanager[label="GenerateMID"];
  
  vod_reportupload -> "topic:_data_vod_event"[style="dashed,filled",label="指标落CK"];
  vod_reportupload -> VodProcess[label="SubmitCapabilityTask"];
  vod_reportupload -> VodProcess[label="SubmitFlowTask"];
  vod_reportupload -> mediamanager[label="CreateVdieoInfo"];
  vod_reportupload -> mediamanager[label="CreateMediaInfo"];
  
  img_preupload -> GenerateImgId;
  img_preupload -> Strategyservice[style="dashed,filled"];
  img_preupload -> mediamanager[label="CheckFingerprint"];
  
  img_notify -> imageserver[label="submitTask"];
  img_notify -> mediamanager[label="SubmitFingerprint"];
}
```
- 新架构逻辑
```
digraph G {
 
  upload_api -> vod_preupload;
  upload_api -> img_preupload;
  upload_api -> vod_reportupload;
  upload_api -> img_notify;
  
  vod_preupload -> GenerateImgId;
  vod_preupload -> Strategyservice[style="dashed,filled"];
  vod_preupload -> mediamanager[label=""];

  vod_reportupload -> VodProcess[label="vod实时提交(100-x %)"];
  vod_reportupload -> mediamanager[label="vod实时提交(100-x %)"];
  
  img_preupload -> GenerateImgId;
  img_preupload -> Strategyservice[style="dashed,filled"];
  img_preupload -> mediamanager[label="CheckFingerprint",style="dashed,filled", color="red"];
  
  img_notify -> imageserver[label="img实时提交"];
  img_notify -> mediamanager[label="SubmitFingerprint",style="dashed,filled", color="red"];

  "kafka:downgrade_upload_request"[style="dashed,filled"];
  "kafka:vod_async_submit_task"[style="dashed,filled"];
  upload_api -> "kafka:downgrade_upload_request"[label="vod异步提交(x%)", color="red"];
  "kafka:downgrade_upload_request" -> upload_api[label="vod异步消费", color="red"];
  vod_reportupload -> "kafka:vod_async_submit_task"[label="vod异步提交任务流", color="red"];
  "kafka:vod_async_submit_task" -> VodProcess[label="vod异步消费", color="red"];
}
```