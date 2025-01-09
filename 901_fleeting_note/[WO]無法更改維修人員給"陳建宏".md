---  
alias:  
creation_date: 2024-06-11 13:12  
tags: 
related:
- [[_wemo_tickets]]
- [docs.google.com/document/d/1Jn1tbzwsHHHmpmFKGrwZ0IHypsxg6PJu8bz2-e4jCAI/edit#heading=h.7s4d2gyslv8c](https://docs.google.com/document/d/1Jn1tbzwsHHHmpmFKGrwZ0IHypsxg6PJu8bz2-e4jCAI/edit#heading=h.7s4d2gyslv8c)
- [[ops_excel_對應_db]]
---  

asana: [Log in - Asana](https://app.asana.com/0/1206177962100119/1207491194918425/f)



# check API error
why error?

`user can't be assigned | progressing tasks over 20`


code


```ts



export const execWorOrderStatuses = [WorkOrderStatus.waitForCheck, WorkOrderStatus.waitForRepair]
export const execAccidentWorOrderStatuses = [WorkOrderStatus.waitForEvaluate]


export enum WorkOrderStatus {
  // ...
  waitForCheck = 2, //待檢測
  waitForRepair = 3, //待維修
  waitForEvaluate = 12, //待評估
  //...
}



const countOfWorkerDoingTasks = await this.repository.count({
where: workOrdersOld.isAccident
  ? { evaluatorId, status: In(execAccidentWorOrderStatuses) }
  : { repairerId, status: In(execWorOrderStatuses) }
})
if (countOfWorkerDoingTasks >= limitOfWorkerDoingTasks) {
throw new Error(`user can't be assigned | progressing tasks over ${limitOfWorkerDoingTasks}`)
}




```



talos accss
[Elastic](https://kibana.wemoscooter.com/goto/af1946b0-27c3-11ef-b8ba-3faa7fd6ccce)

men-api access
[Elastic](https://kibana.wemoscooter.com/goto/a7791110-27c3-11ef-b8ba-3faa7fd6ccce)

men-api *
[Elastic](https://kibana.wemoscooter.com/goto/9d2999f0-27c3-11ef-b8ba-3faa7fd6ccce)
![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003104933778.webp]]











# 查 db 兩個人的權限 -> 但不是問題點

陳建宏  and 陶志強 是否權限一樣


陳建宏
273
jianhong.chen@service.wemoscooter.com
![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003104948766.webp|1025]]
![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105001151.webp]]

![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105018213.webp]]
![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105044273.webp]]


陶志強
466
chihchiang.tao@service.wemoscooter.com
![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105044560.webp]]

![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105106945.webp]]

都是台北五股小組長

![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105127130.webp]]

![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105127309.webp]]


# 結論

陳和陶都是台北五股小組長
但是陳有檢測員的權限，陶沒有



# QAT環境


ben Hu -> mgm id -> 352

prod is 83
qat is 242

五股維修中心

![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105136403.webp]]


![[IMG-[WO]無法更改維修人員給"陳建宏"-20241003105136591.webp]]



更改打的 API?
Request URL:
https://apollo-qat.wemoscooter.com/talos/v1/workOrders

Request Method:
PUT

payload
{"event":"EDIT","workOrderIds":["278833"],"comment":"test","repairerId":"352","officeId":"238"}