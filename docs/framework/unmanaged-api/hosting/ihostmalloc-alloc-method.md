---
description: 了解详细信息： IHostMAlloc：：分配方法
title: IHostMAlloc::Alloc 方法
ms.date: 03/30/2017
api_name:
- IHostMAlloc.Alloc
api_location:
- mscoree.dll
api_type:
- COM
f1_keywords:
- IHostMAlloc::Alloc
helpviewer_keywords:
- Alloc method, IHostMAlloc interface [.NET Framework hosting]
- IHostMAlloc::Alloc method [.NET Framework hosting]
ms.assetid: a3007f5e-d75d-4b37-842b-704e9edced5e
topic_type:
- apiref
ms.openlocfilehash: e0349c273ef9e3194bb8bad167510dd8fefcab62
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99708224"
---
# <a name="ihostmallocalloc-method"></a>IHostMAlloc::Alloc 方法

请求宿主从堆分配指定内存量。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT Alloc (  
    [in] SIZE_T  cbSize,
    [in] EMemoryCriticalLevel dwCriticalLevel,
    [out] void** ppMem  
);  
```  
  
## <a name="parameters"></a>参数  

 `cbSize`  
 中当前内存分配请求的大小（以字节为单位）。  
  
 `dwCriticalLevel`  
 中 [EMemoryCriticalLevel](ememorycriticallevel-enumeration.md) 值之一，指示分配失败的影响。  
  
 `ppMem`  
 弄指向分配的内存的指针; 如果请求无法完成，则为 null。  
  
## <a name="return-value"></a>返回值  
  
|HRESULT|说明|  
|-------------|-----------------|  
|S_OK|`Alloc` 已成功返回。|  
|HOST_E_CLRNOTAVAILABLE| (CLR) 的公共语言运行时未加载到进程中，或 CLR 处于无法运行托管代码或成功处理调用的状态。|  
|HOST_E_TIMEOUT|调用超时。|  
|HOST_E_NOT_OWNER|调用方不拥有该锁。|  
|HOST_E_ABANDONED|已阻止的线程或纤程正在等待某个事件时，该事件被取消。|  
|E_FAIL|发生未知的灾难性故障。 当方法返回 E_FAIL 时，CLR 在该进程内将不再可用。 对宿主方法的后续调用会返回 HOST_E_CLRNOTAVAILABLE。|  
|E_OUTOFMEMORY|没有足够的内存可用来完成分配请求。|  
  
## <a name="remarks"></a>备注  

 CLR `IHostMalloc` 通过调用 [IHostMemoryManager：： CreateMalloc](ihostmemorymanager-createmalloc-method.md) 方法获取指向实例的接口指针。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **标头：** Mscoree.dll  
  
 **库：** 作为中的资源包含 MSCorEE.dll  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>请参阅

- [IHostMemoryManager 接口](ihostmemorymanager-interface.md)
- [IHostMalloc 接口](ihostmalloc-interface.md)
