---
description: 了解详细信息： 106-CancelRequestRecord
title: 106 - CancelRequestRecord
ms.date: 03/30/2017
ms.assetid: f72a59aa-8093-4a8e-94df-40acaffb1ffb
ms.openlocfilehash: a5d65ef8606821dc8aa7b64b36498b343ff986e2
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99667651"
---
# <a name="106---cancelrequestrecord"></a>106 - CancelRequestRecord

## <a name="properties"></a>属性  
  
|||  
|-|-|  
|ID|106|  
|关键字|EndToEndMonitoring、Troubleshooting、HealthMonitoring、WFTracking|  
|级别|信息|  
|通道|Microsoft-Windows-应用程序服务器-应用程序/分析|  
  
## <a name="description"></a>说明  

 当工作流实例中的某个活动发出 cancelrequestedrecord 时，ETW 跟踪参与者将发出此事件。  
  
## <a name="message"></a>消息  

 TrackRecord = CancelRequestedRecord, InstanceID=%1, RecordNumber=%2, EventTime=%3, Name=%4, ActivityId=%5, ActivityInstanceId=%6, ActivityTypeName = %7, ChildActivityName = %8, ChildActivityId = %9, ChildActivityInstanceId = %10, ChildActivityTypeName =%11, Annotations=%12, ProfileName = %13  
  
## <a name="details"></a>详细信息  
  
|数据项名称|数据项类型|说明|  
|--------------------|--------------------|-----------------|  
|InstanceId|xs:GUID|工作流的实例 ID|  
|RecordNumber|xs:long|发出的记录的序列号|  
|EventTime|xs:dateTime|发出该事件时的 UTC 时间|  
|名称|xs:string|请求取消操作的活动的名称|  
|ActivityId|xs:string|请求取消操作的活动的 ID|  
|ActivityInstanceId|xs:string|请求取消操作的活动的实例 ID|  
|ActivityTypeName|xs:string|请求取消操作的活动的类型|  
|ChildActivityName|xs:string|所取消的活动的名称|  
|ChildActivityId|xs:string|所取消的活动的 ID|  
|ChildActivityInstanceId|xs:string|所取消的活动的实例 ID|  
|ChildActivityTypeName|xs:string|所取消的活动的类型|  
|批注|xs:string|已添加到此事件中的批注。  值存储在 xml 元素中，格式为 \<items> \< item  name = "annotationName" type="System.String"> a \</item> \</items> 。  如果未指定任何批注，则该字符串包含 \<items/> 。 ETW 事件大小受到 ETW 缓冲区大小或 ETW 事件最大负载的限制。 如果事件的大小超过 ETW 限制，则通过删除批注并将批注值替换为 ... 来截断事件。 \<items> \</items>|  
|ProfileName|xs:string|导致发出此事件的跟踪配置文件的名称|  
|HostReference|xs:string|对于 Web 承载的服务，此字段唯一标识 Web 层次结构中的服务。  其格式定义为 "网站名称应用程序虚拟路径&#124;服务虚拟路径&#124;ServiceName" 示例： "Default Web Site//Calculatorapplication&#124;/CalculatorService.svc&#124;CalculatorService"|  
|应用程序域|xs:string|由 AppDomain.CurrentDomain.FriendlyName 返回的字符串。|
