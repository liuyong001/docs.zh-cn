---
description: 了解详细信息： ICorPublishProcessEnum 接口
title: ICorPublishProcessEnum 接口
ms.date: 03/30/2017
api_name:
- ICorPublishProcessEnum
api_location:
- mscordbi.dll
api_type:
- COM
f1_keywords:
- ICorPublishProcessEnum
helpviewer_keywords:
- ICorPublishProcessEnum interface [.NET Framework debugging]
ms.assetid: aac8fcf9-ac09-437c-bd5c-2fda14ae1007
topic_type:
- apiref
ms.openlocfilehash: 87d80d066995dbeca67f461e01652dd3deb3bf1b
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99790485"
---
# <a name="icorpublishprocessenum-interface"></a>ICorPublishProcessEnum 接口

[ICorPublishEnum](icorpublishenum-interface.md)接口的子类，提供遍历[ICorPublishProcess](icorpublishprocess-interface.md)对象集合的方法。  
  
## <a name="methods"></a>方法  
  
|方法|说明|  
|------------|-----------------|  
|[下一方法](icorpublishprocessenum-next-method.md)|`ICorPublishProcess`从当前位置开始，获取集合中指定数量的实例。|  
  
## <a name="remarks"></a>备注  

 `ICorPublishProcessEnum`接口实现抽象接口的方法[ICorPublishEnum](icorpublishenum-interface.md)。  
  
 `ICorPublishProcessEnum`实例由[ICorPublish：： EnumProcesses](icorpublish-enumprocesses-method.md)方法创建。 对象集合的遍历 `ICorPublishProcess` 基于创建实例时给定的筛选条件 `ICorPublishProcessEnum` 。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **标头：** CorPub，CorPub  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v10plus](../../../../includes/net-current-v10plus-md.md)]  
  
## <a name="see-also"></a>请参阅

- [调试接口](debugging-interfaces.md)
- [CorpubPublish Coclass](corpubpublish-coclass.md)
