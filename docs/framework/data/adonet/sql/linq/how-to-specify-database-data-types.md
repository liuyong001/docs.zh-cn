---
description: 了解详细信息：如何：指定数据库数据类型
title: 如何：指定数据库数据类型
ms.date: 03/30/2017
ms.assetid: 2228fdad-7e6a-4b1b-b4d1-79d0198b7c28
ms.openlocfilehash: 004a16fe9dd0cda608485b13b03ce922d6a9f671
ms.sourcegitcommit: ddf7edb67715a5b9a45e3dd44536dabc153c1de0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99785908"
---
# <a name="how-to-specify-database-data-types"></a>如何：指定数据库数据类型

使用 [!INCLUDE[vbtecdlinq](../../../../../../includes/vbtecdlinq-md.md)] <xref:System.Data.Linq.Mapping.ColumnAttribute.DbType%2A> 特性上的属性 <xref:System.Data.Linq.Mapping.ColumnAttribute> 可以指定在 t-sql 表声明中定义列的确切文本。  
  
 仅当您打算使用 <xref:System.Data.Linq.Mapping.ColumnAttribute.DbType%2A> 来创建数据库实例时，才必须指定 <xref:System.Data.Linq.DataContext.CreateDatabase%2A> 属性。  
  
 有关代码示例，请参见<xref:System.Data.Linq.Mapping.ColumnAttribute.DbType%2A>。  
  
### <a name="to-specify-text-to-define-a-data-type-in-a-t-sql-table"></a>指定用于定义 T-SQL 表中数据类型的文本  
  
1. 将 <xref:System.Data.Linq.Mapping.ColumnAttribute.DbType%2A> 属性 (Property) 添加到 <xref:System.Data.Linq.Mapping.ColumnAttribute> 属性 (Attribute)。  
  
2. 将 <xref:System.Data.Linq.Mapping.ColumnAttribute.DbType%2A> 属性的值设置为 T-SQL 使用的确切文本。  
  
## <a name="see-also"></a>请参阅

- [LINQ to SQL 对象模型](the-linq-to-sql-object-model.md)
- [如何：通过使用代码编辑器自定义实体类](how-to-customize-entity-classes-by-using-the-code-editor.md)
