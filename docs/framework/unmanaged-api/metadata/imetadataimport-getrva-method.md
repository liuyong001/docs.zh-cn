---
description: 了解详细信息： IMetaDataImport：： GetRVA 方法
title: IMetaDataImport::GetRVA 方法
ms.date: 03/30/2017
api_name:
- IMetaDataImport.GetRVA
api_location:
- mscoree.dll
api_type:
- COM
f1_keywords:
- IMetaDataImport::GetRVA
helpviewer_keywords:
- GetRVA method [.NET Framework metadata]
- IMetaDataImport::GetRVA method [.NET Framework metadata]
ms.assetid: ea422217-988b-4acd-b2db-c55357938275
topic_type:
- apiref
ms.openlocfilehash: 8d6439bcad50a6311e7bb1408f4c86144a5026ce
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99789159"
---
# <a name="imetadataimportgetrva-method"></a>IMetaDataImport::GetRVA 方法

获取指定标记所表示的方法或字段的相对虚拟地址 (RVA) 和实现标志。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT GetRVA (  
   [in]  mdToken     tk,
   [out] ULONG       *pulCodeRVA,
   [out]  DWORD      *pdwImplFlags  
);  
```  
  
## <a name="parameters"></a>参数  

 `tk`  
 中一个 MethodDef 或 FieldDef 元数据标记，它表示要为其返回 RVA 的代码对象。 如果标记为 FieldDef，则字段必须是全局变量。  
  
 `pulCodeRVA`  
 弄指向由标记表示的代码对象的相对虚拟地址的指针。  
  
 `pdwImplFlags`  
 弄一个指针，指向方法的实现标志。 此值是 [CorMethodImpl](cormethodimpl-enumeration.md) 枚举中的位掩码。 `pdwImplFlags`仅当为 MethodDef 标记时，的值才有效 `tk` 。  
  
## <a name="requirements"></a>要求  

 **平台：** 请参阅 [系统要求](../../get-started/system-requirements.md)。  
  
 **标头：** Cor  
  
 **库：** 作为中的资源包含 MsCorEE.dll  
  
 **.NET Framework 版本：**[!INCLUDE[net_current_v10plus](../../../../includes/net-current-v10plus-md.md)]  
  
## <a name="see-also"></a>请参阅

- [IMetaDataImport 接口](imetadataimport-interface.md)
- [IMetaDataImport2 接口](imetadataimport2-interface.md)
