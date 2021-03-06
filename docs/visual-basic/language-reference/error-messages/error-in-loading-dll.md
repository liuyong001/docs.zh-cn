---
description: '了解详细信息：加载 DLL 时出错 (Visual Basic) '
title: 加载 DLL 时出错
ms.date: 07/20/2015
f1_keywords:
- vbrID48
ms.assetid: 4226cd1f-028c-477d-88a5-cb57f7e0cdc8
ms.openlocfilehash: 098d05e93d328f3667000bd81290f4b77cf7949e
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99796504"
---
# <a name="error-in-loading-dll-visual-basic"></a>加载 DLL 时出错 (Visual Basic)

动态链接库 (DLL) 是语句的子句中指定的库 `Lib` `Declare` 。 此错误的可能原因包括：  
  
- 该文件不是 DLL 可执行文件。  
  
- 该文件不是 Microsoft Windows DLL。  
  
- DLL 引用了不存在的另一个 DLL。  
  
- DLL 或被引用的 DLL 不在路径中指定的目录中。  
  
## <a name="to-correct-this-error"></a>更正此错误  
  
- 如果该文件是源文本文件而不是 DLL 可执行文件，则必须对其进行编译并将其链接到 DLL 可执行文件形式。  
  
- 如果文件不是 Microsoft Windows DLL，请获取 Microsoft Windows 等效项。  
  
- 如果 DLL 引用了不存在的另一个 DLL，则获取被引用的 DLL 并使其可用。  
  
- 如果 DLL 或被引用的 DLL 不在路径所指定的目录中，则将 DLL 移到引用的目录。  
  
## <a name="see-also"></a>请参阅

- [Declare Statement](../statements/declare-statement.md)
