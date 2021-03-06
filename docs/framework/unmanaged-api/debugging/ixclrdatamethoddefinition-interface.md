---
description: 了解详细信息： IXCLRDataMethodDefinition 接口
title: IXCLRDataMethodDefinition 接口
ms.date: 01/16/2019
api.name:
- IXCLRDataMethodDefinition Interface
api.location:
- mscordacwks.dll
api.type:
- COM
f1.keywords:
- IXCLRDataMethodDefinition Interface
helpviewer.keywords:
- IXCLRDataMethodDefinition Interface [.NET Framework debugging]
topic_type:
- apiref
author: cshung
ms.author: andrewau
ms.openlocfilehash: d73246b42a0bfb982c87b04d5490e945f7caa745
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99790342"
---
# <a name="ixclrdatamethoddefinition-interface"></a>IXCLRDataMethodDefinition 接口

提供用于查询有关方法定义的信息的方法。

[!INCLUDE[debugging-api-recommended-note](../../../../includes/debugging-api-recommended-note.md)]

## <a name="methods"></a>方法

以下方法是接口中的一些可用方法。

| 方法                                                                                                                          | 说明                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| [StartEnumInstances](ixclrdatamethoddefinition-startenuminstances-method.md) | 为给定的的方法实例枚举提供句柄 `IXCLRDataAppDomain` 。 |
| [EnumInstance](ixclrdatamethoddefinition-enuminstance-method.md)             | 枚举此方法定义的实例。                                         |
| [EndEnumInstances](ixclrdatamethoddefinition-endenuminstances-method.md)     | 释放实例枚举期间使用的内部迭代器所使用的资源。         |

## <a name="remarks"></a>备注

此接口在运行时中存在，不会通过任何标头或库文件公开。 但是，它是从使用 GUID 派生的 COM 接口， `IUnknown` `AAF60008-FB2C-420b-8FB1-42D244A54A97` 该接口可通过常用的 COM 机制获得。

## <a name="requirements"></a>要求

**平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
**标头：** 内容  
**库：** 内容  
**.NET Framework 版本：**[!INCLUDE[net_current_v47plus](../../../../includes/net-current-v47plus.md)]  

## <a name="see-also"></a>请参阅

- [调试](index.md)
- [调试接口](debugging-interfaces.md)
