---
title: 从 .NET for Apache Spark 应用程序调用 Java UDF
description: 了解如何从 .NET for Apache Spark 应用程序调用 Java UDF。
ms.author: nidutta
author: Niharikadutta
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: mvc,how-to
ms.openlocfilehash: edf525102bf5503dcb51247b5fa590aa0d42b369
ms.sourcegitcommit: 67ebdb695fd017d79d9f1f7f35d145042d5a37f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92224116"
---
# <a name="call-a-java-udf-from-your-net-for-apache-spark-application"></a><span data-ttu-id="668b6-103">从 .NET for Apache Spark 应用程序调用 Java UDF</span><span class="sxs-lookup"><span data-stu-id="668b6-103">Call a Java UDF from your .NET for Apache Spark application</span></span>

<span data-ttu-id="668b6-104">本文介绍如何从 [.NET for Apache Spark](https://github.com/dotnet/spark) 应用程序调用 Java 用户定义的函数 (UDF)。</span><span class="sxs-lookup"><span data-stu-id="668b6-104">In this article, you learn how to call a Java User-Defined Function (UDF) from your [.NET for Apache Spark](https://github.com/dotnet/spark) application.</span></span>

1. <span data-ttu-id="668b6-105">如何定义 Java UDF 并将其编译成 jar - 如果已在 jar 文件中定义了 UDF，则不需要执行此步骤。</span><span class="sxs-lookup"><span data-stu-id="668b6-105">How to define your Java UDFs and compile them into a jar - this step is not needed if you already have a UDF defined in a jar file.</span></span> <span data-ttu-id="668b6-106">在这种情况下，你只需要包含包的 UDF 函数的全名。</span><span class="sxs-lookup"><span data-stu-id="668b6-106">In which case, all you need is the full name of the UDF function including the package.</span></span>
2. <span data-ttu-id="668b6-107">在 .NET for Apache Spark 应用程序中注册并调用 Java UDF。</span><span class="sxs-lookup"><span data-stu-id="668b6-107">Register and call your Java UDF in your .NET for Apache Spark application.</span></span>

## <a name="define-and-compile-your-java-udfs"></a><span data-ttu-id="668b6-108">定义和编译 Java UDF</span><span class="sxs-lookup"><span data-stu-id="668b6-108">Define and compile your Java UDFs</span></span>

1. <span data-ttu-id="668b6-109">创建 Maven 或 SBT 项目，并将以下依赖项添加到项目配置文件中：</span><span class="sxs-lookup"><span data-stu-id="668b6-109">Create a Maven or SBT project and add the following dependencies into the project configuration file:</span></span>
    1. `org.apache.spark.spark-core_2.11.<version>`
    2. `org.apache.spark.spark-sql_2.11.<version>`
2. <span data-ttu-id="668b6-110">定义 Java UDF，具体方法是实现[相关接口](https://github.com/apache/spark/blob/master/sql/core/src/main/java/org/apache/spark/sql/api/java/UDF1.java)（根据 UDF 的签名）并导入相关的包，如下面的简单示例所示</span><span class="sxs-lookup"><span data-stu-id="668b6-110">Define your Java UDF by implementing the [relevant interface](https://github.com/apache/spark/blob/master/sql/core/src/main/java/org/apache/spark/sql/api/java/UDF1.java) (according to your UDF's signature) and importing the relevant package as shown below in a simple example</span></span>

    ```java
    package com.ScalaUdf.app; // Name of package where UDF is defined
    import org.apache.spark.sql.api.java.UDF1; // UDF interface to implement

    public class JavaUdf implements UDF1<Integer, Integer> { // Name of the Java UDF
        private static final int serialVersionUID = 1;
        @Override
        public Integer call(Integer num) throws Exception { // Define logic of UDF
            return (num + 5);
        }
    }
    ```

3. <span data-ttu-id="668b6-111">编译并打包项目，以创建可执行的 jar，如 `UdfApp-0.0.1.jar`。</span><span class="sxs-lookup"><span data-stu-id="668b6-111">Compile and package your project to create and executable jar say `UdfApp-0.0.1.jar`.</span></span>

## <a name="register-and-call-java-udfs-in-net-for-apache-spark"></a><span data-ttu-id="668b6-112">在 .NET for Apache Spark 中注册和调用 Java UDF</span><span class="sxs-lookup"><span data-stu-id="668b6-112">Register and call Java UDFs in .NET for Apache Spark</span></span>

1. <span data-ttu-id="668b6-113">使用 [`RegisterJava`](https://github.com/dotnet/spark/blob/8dcdcdc7c60d5f42cba5a90f1346d854ab5bf7bb/src/csharp/Microsoft.Spark/Sql/UDFRegistration.cs#L424) API 将 Java UDF 注册到 Spark SQL 中。</span><span class="sxs-lookup"><span data-stu-id="668b6-113">Use the [`RegisterJava`](https://github.com/dotnet/spark/blob/8dcdcdc7c60d5f42cba5a90f1346d854ab5bf7bb/src/csharp/Microsoft.Spark/Sql/UDFRegistration.cs#L424) API to register your Java UDF with Spark SQL.</span></span>
2. <span data-ttu-id="668b6-114">使用 [`CreateOrReplaceTempView`](https://github.com/dotnet/spark/blob/master/src/csharp/Microsoft.Spark/Sql/DataFrame.cs#L982) 函数，注册要将 UDF 作为 SQL 表调用的 `DataFrame`。</span><span class="sxs-lookup"><span data-stu-id="668b6-114">Register the `DataFrame` on which you want to call your UDF as an SQL Table using the [`CreateOrReplaceTempView`](https://github.com/dotnet/spark/blob/master/src/csharp/Microsoft.Spark/Sql/DataFrame.cs#L982) function.</span></span>
3. <span data-ttu-id="668b6-115">使用 `SparkSession.Sql` 调用使用 Spark SQL 的表视图上的 UDF。</span><span class="sxs-lookup"><span data-stu-id="668b6-115">Use `SparkSession.Sql` to call the UDF on the table view using Spark SQL.</span></span>
<span data-ttu-id="668b6-116">说明以上步骤的基本示例：</span><span class="sxs-lookup"><span data-stu-id="668b6-116">A basic example to illustrate the above steps:</span></span>

    ```csharp
    class Program
    {
        static void Main()
        {
            SparkSession spark = SparkSession
                .Builder()
                .AppName("Scala/Java UDFs from .NET for Apache Spark")
                .GetOrCreate();
            spark.Udf().RegisterJava<int>("udfAdd5", "com.ScalaUdf.app.JavaUdf"); // Register your Java UDF as 'udfAdd5'
            DataFrame df = spark.CreateDataFrame(new int[] { 2, 5 });
            df.CreateOrReplaceTempView("numbersData"); // Create an SQL table from the DataFrame `df`
            DataFrame dfUdf = spark.Sql("SELECT udfAdd5(_1) As Result FROM numbersData"); // Call the registered UDF on the table
            dfUdf.Show();
            spark.Stop();
        }
    }
    ```

4. <span data-ttu-id="668b6-117">通过 `--jars` 选项传递先前编译的 Java UDF jar，以使用 `spark-submit` 提交此应用程序：</span><span class="sxs-lookup"><span data-stu-id="668b6-117">Submit this application using `spark-submit` by passing the previously compiled Java UDF jar through the `--jars` option:</span></span>

    ```bash
    spark-submit --master local --jars UdfApp-0.0.1.jar --class org.apache.spark.deploy.dotnet.DotnetRunner microsoft-spark-3.0.x-0.12.1.jar InterRuntimeUDFs.exe
    ```

    <span data-ttu-id="668b6-118">生成的 `dfUdf` DataFrame 将数字 5 添加到 `JavaUdf` 定义的输入列的每一行：</span><span class="sxs-lookup"><span data-stu-id="668b6-118">The resultant `dfUdf` DataFrame had the number 5 added to each row of the input column as defined by `JavaUdf`:</span></span>

    ```text
    +-------+
    | Result|
    +-------+
    |      7|
    |     10|
    +-------+
    ```

## <a name="call-net-udf-from-scala-or-python-in-apache-spark"></a><span data-ttu-id="668b6-119">在 Apache Spark 中从 Scala 或 Python 调用 .NET UDF</span><span class="sxs-lookup"><span data-stu-id="668b6-119">Call .NET UDF from Scala or Python in Apache Spark</span></span>

<span data-ttu-id="668b6-120">你还可以使用 [sparkdotnetudf 开源工具](https://github.com/imback82/sparkdotnetudf)从用 Scala 或 Python 编写的 Apache Spark 应用程序注册和调用 C# UDF。</span><span class="sxs-lookup"><span data-stu-id="668b6-120">You can also register and invoke a C# UDF from an Apache Spark application written in Scala or Python using [the sparkdotnetudf open source tool](https://github.com/imback82/sparkdotnetudf).</span></span>