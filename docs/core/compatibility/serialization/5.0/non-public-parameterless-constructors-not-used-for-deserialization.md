---
title: 中断性变更：非公共的无参数构造函数不用于反序列化
description: 了解 .NET 5.0 中的以下中断性变更：非公共的无参数构造函数不再用于 JsonSerializer 的反序列化。
ms.date: 10/18/2020
ms.openlocfilehash: 6bdcc92c61008aa4ee27370bbac4dbf4ee3ef7c8
ms.sourcegitcommit: d8020797a6657d0fbbdff362b80300815f682f94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95759274"
---
# <a name="non-public-parameterless-constructors-not-used-for-deserialization"></a><span data-ttu-id="4e08e-103">非公共的无参数构造函数不用于反序列化</span><span class="sxs-lookup"><span data-stu-id="4e08e-103">Non-public, parameterless constructors not used for deserialization</span></span>

<span data-ttu-id="4e08e-104">为了在所有受支持的目标框架名字对象 (TFM) 之间保持一致，默认情况下，非公共的无参数构造函数不再用于 <xref:System.Text.Json.JsonSerializer> 的反序列化。</span><span class="sxs-lookup"><span data-stu-id="4e08e-104">For consistency across all supported target framework monikers (TFMs), non-public, parameterless constructors are no longer used for deserialization with <xref:System.Text.Json.JsonSerializer>, by default.</span></span>

## <a name="change-description"></a><span data-ttu-id="4e08e-105">更改描述</span><span class="sxs-lookup"><span data-stu-id="4e08e-105">Change description</span></span>

<span data-ttu-id="4e08e-106">支持 .NET Standard 2.0 及更高版本（即版本 4.6.0 - 4.7.2）的独立 [System.Text.Json NuGet 包](https://www.nuget.org/packages/System.Text.Json/)的行为与 .NET Core 3.0 和 3.1 上的内置行为不一致。</span><span class="sxs-lookup"><span data-stu-id="4e08e-106">The standalone [System.Text.Json NuGet packages](https://www.nuget.org/packages/System.Text.Json/) that support .NET Standard 2.0 and higher, that is, versions 4.6.0-4.7.2, behave inconsistently with the built-in behavior on .NET Core 3.0 and 3.1.</span></span> <span data-ttu-id="4e08e-107">在 .NET Core 3.x 上，内部和专用构造函数可用于反序列化。</span><span class="sxs-lookup"><span data-stu-id="4e08e-107">On .NET Core 3.x, internal and private constructors can be used for deserialization.</span></span> <span data-ttu-id="4e08e-108">在独立包中，不允许使用非公共构造函数，如果未定义任何公共的无参数构造函数，则会引发 <xref:System.MissingMethodException>。</span><span class="sxs-lookup"><span data-stu-id="4e08e-108">In the standalone packages, non-public constructors are not allowed, and a <xref:System.MissingMethodException> is thrown if no public, parameterless constructor is defined.</span></span>

<span data-ttu-id="4e08e-109">从 .NET 5.0 和 System.Text.Json NuGet 包 5.0.0 开始，NuGet 包与内置 API 的行为一致。</span><span class="sxs-lookup"><span data-stu-id="4e08e-109">Starting with .NET 5.0 and System.Text.Json NuGet package 5.0.0, the behavior is consistent between the NuGet package and the built-in APIs.</span></span> <span data-ttu-id="4e08e-110">默认情况下，序列化程序会忽略非公共构造函数，包括无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="4e08e-110">Non-public constructors, including parameterless constructors, are ignored by the serializer by default.</span></span> <span data-ttu-id="4e08e-111">序列化程序使用下面一种构造函数进行反序列化：</span><span class="sxs-lookup"><span data-stu-id="4e08e-111">The serializer uses one of the following constructors for deserialization:</span></span>

- <span data-ttu-id="4e08e-112">用 <xref:System.Text.Json.Serialization.JsonConstructorAttribute> 注释的公共构造函数。</span><span class="sxs-lookup"><span data-stu-id="4e08e-112">Public constructor annotated with <xref:System.Text.Json.Serialization.JsonConstructorAttribute>.</span></span>
- <span data-ttu-id="4e08e-113">公共无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="4e08e-113">Public parameterless constructor.</span></span>
- <span data-ttu-id="4e08e-114">公共参数化构造函数（如果它是唯一的公共构造函数）。</span><span class="sxs-lookup"><span data-stu-id="4e08e-114">Public parameterized constructor (if it's the only public constructor present).</span></span>

<span data-ttu-id="4e08e-115">如果这些构造函数都不可用，则在尝试对该类型进行反序列化时，将引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="4e08e-115">If none of these constructors are available, a <xref:System.NotSupportedException> is thrown if you attempt to deserialize the type.</span></span>

## <a name="version-introduced"></a><span data-ttu-id="4e08e-116">引入的版本</span><span class="sxs-lookup"><span data-stu-id="4e08e-116">Version introduced</span></span>

<span data-ttu-id="4e08e-117">5.0</span><span class="sxs-lookup"><span data-stu-id="4e08e-117">5.0</span></span>

## <a name="reason-for-change"></a><span data-ttu-id="4e08e-118">更改原因</span><span class="sxs-lookup"><span data-stu-id="4e08e-118">Reason for change</span></span>

- <span data-ttu-id="4e08e-119">为了在 <xref:System.Text.Json?displayProperty=fullName> 针对其生成的所有目标框架名字对象 (TFM) 之间强制执行一致的行为（.NET Core 3.0 和更高版本以及 .NET Standard 2.0）</span><span class="sxs-lookup"><span data-stu-id="4e08e-119">To enforce consistent behavior between all target framework monikers (TFMs) that <xref:System.Text.Json?displayProperty=fullName> builds for (.NET Core 3.0 and later versions and .NET Standard 2.0)</span></span>
- <span data-ttu-id="4e08e-120">因为 <xref:System.Text.Json.JsonSerializer> 不应调用某类型的非公共外围应用，无论它是构造函数、属性还是字段。</span><span class="sxs-lookup"><span data-stu-id="4e08e-120">Because <xref:System.Text.Json.JsonSerializer> shouldn't call the non-public surface area of a type, whether that's a constructor, a property, or a field.</span></span>

## <a name="recommended-action"></a><span data-ttu-id="4e08e-121">建议的操作</span><span class="sxs-lookup"><span data-stu-id="4e08e-121">Recommended action</span></span>

- <span data-ttu-id="4e08e-122">如果你拥有该类型并且这样可行，则将无参数构造函数设为公共。</span><span class="sxs-lookup"><span data-stu-id="4e08e-122">If you own the type and it's feasible, make the parameterless constructor public.</span></span>
- <span data-ttu-id="4e08e-123">否则，针对该类型实现 `JsonConverter<T>`，并控制反序列化行为。</span><span class="sxs-lookup"><span data-stu-id="4e08e-123">Otherwise, implement a `JsonConverter<T>` for the type and control the deserialization behavior.</span></span>

## <a name="affected-apis"></a><span data-ttu-id="4e08e-124">受影响的 API</span><span class="sxs-lookup"><span data-stu-id="4e08e-124">Affected APIs</span></span>

- <xref:System.Text.Json.JsonSerializer.Deserialize%2A?displayProperty=fullName>
- <xref:System.Text.Json.JsonSerializer.DeserializeAsync%2A?displayProperty=fullName>

<!--

### Affected APIs

- `Overload:System.Text.Json.JsonSerializer.Deserialize`
- `Overload:System.Text.Json.JsonSerializer.DeserializeAsync`

### Category

Serialization

-->