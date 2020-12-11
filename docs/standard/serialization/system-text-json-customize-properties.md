---
title: 如何使用 System.Text.Json 自定义属性名称和值
description: 了解使用 .NET 中的 System.Text.Json 进行序列化时如何自定义属性名称和值。
ms.date: 11/30/2020
no-loc:
- System.Text.Json
- Newtonsoft.Json
helpviewer_keywords:
- JSON serialization
- serializing objects
- serialization
- objects, serializing
ms.openlocfilehash: c754d41071e886bc1efcc3a30e249bf9e554ab5b
ms.sourcegitcommit: 9d525bb8109216ca1dc9e39c149d4902f4b43da5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96599585"
---
# <a name="how-to-customize-property-names-and-values-with-no-locsystemtextjson"></a><span data-ttu-id="15233-103">如何使用 System.Text.Json 自定义属性名称和值</span><span class="sxs-lookup"><span data-stu-id="15233-103">How to customize property names and values with System.Text.Json</span></span>

<span data-ttu-id="15233-104">默认情况下，属性名称和字典键在 JSON 输出中保持不变，包括大小写。</span><span class="sxs-lookup"><span data-stu-id="15233-104">By default, property names and dictionary keys are unchanged in the JSON output, including case.</span></span> <span data-ttu-id="15233-105">枚举值表示为数字。</span><span class="sxs-lookup"><span data-stu-id="15233-105">Enum values are represented as numbers.</span></span> <span data-ttu-id="15233-106">本文将指导如何进行以下操作：</span><span class="sxs-lookup"><span data-stu-id="15233-106">In this article, you'll learn how to:</span></span>

> [!NOTE]
> <span data-ttu-id="15233-107">[Web 默认值](system-text-json-configure-options.md#web-defaults-for-jsonserializeroptions)为 camel 形式大小写。</span><span class="sxs-lookup"><span data-stu-id="15233-107">The [web default](system-text-json-configure-options.md#web-defaults-for-jsonserializeroptions) is camel case.</span></span>

* [<span data-ttu-id="15233-108">自定义单个属性名称</span><span class="sxs-lookup"><span data-stu-id="15233-108">Customize individual property names</span></span>](#customize-individual-property-names)
* [<span data-ttu-id="15233-109">将所有属性名称转换为 camel 大小写</span><span class="sxs-lookup"><span data-stu-id="15233-109">Convert all property names to camel case</span></span>](#use-camel-case-for-all-json-property-names)
* [<span data-ttu-id="15233-110">实现自定义属性命名策略</span><span class="sxs-lookup"><span data-stu-id="15233-110">Implement a custom property naming policy</span></span>](#use-a-custom-json-property-naming-policy)
* [<span data-ttu-id="15233-111">将字典键转换为 camel 大小写</span><span class="sxs-lookup"><span data-stu-id="15233-111">Convert dictionary keys to camel case</span></span>](#camel-case-dictionary-keys)
* [<span data-ttu-id="15233-112">将枚举转换为字符串和 camel 大小写</span><span class="sxs-lookup"><span data-stu-id="15233-112">Convert enums to strings and camel case</span></span>](#enums-as-strings)

<span data-ttu-id="15233-113">对于需要对 JSON 属性名称和值进行特殊处理的其他方案，可以[实现自定义转换器](system-text-json-converters-how-to.md)。</span><span class="sxs-lookup"><span data-stu-id="15233-113">For other scenarios that require special handling of JSON property names and values, you can [implement custom converters](system-text-json-converters-how-to.md).</span></span>

## <a name="customize-individual-property-names"></a><span data-ttu-id="15233-114">自定义单个属性名称</span><span class="sxs-lookup"><span data-stu-id="15233-114">Customize individual property names</span></span>

<span data-ttu-id="15233-115">若要设置单个属性的名称，请使用 [[JsonPropertyName]](xref:System.Text.Json.Serialization.JsonPropertyNameAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="15233-115">To set the name of individual properties, use the [[JsonPropertyName]](xref:System.Text.Json.Serialization.JsonPropertyNameAttribute) attribute.</span></span>

<span data-ttu-id="15233-116">下面是要进行序列化的示例类型和生成的 JSON：</span><span class="sxs-lookup"><span data-stu-id="15233-116">Here's an example type to serialize and resulting JSON:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/WeatherForecast.cs" id="WFWithPropertyNameAttribute":::

```json
{
  "Date": "2019-08-01T00:00:00-07:00",
  "TemperatureCelsius": 25,
  "Summary": "Hot",
  "Wind": 35
}
```

<span data-ttu-id="15233-117">此特性设置的属性名称：</span><span class="sxs-lookup"><span data-stu-id="15233-117">The property name set by this attribute:</span></span>

* <span data-ttu-id="15233-118">同时适用于两个方向（序列化和反序列化）。</span><span class="sxs-lookup"><span data-stu-id="15233-118">Applies in both directions, for serialization and deserialization.</span></span>
* <span data-ttu-id="15233-119">优先于属性命名策略。</span><span class="sxs-lookup"><span data-stu-id="15233-119">Takes precedence over property naming policies.</span></span>

## <a name="use-camel-case-for-all-json-property-names"></a><span data-ttu-id="15233-120">对所有 JSON 属性名称使用 camel 大小写</span><span class="sxs-lookup"><span data-stu-id="15233-120">Use camel case for all JSON property names</span></span>

<span data-ttu-id="15233-121">若要对所有 JSON 属性名称使用 camel 大小写，请将 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy?displayProperty=nameWithType> 设置为 `JsonNamingPolicy.CamelCase`，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="15233-121">To use camel case for all JSON property names, set <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy?displayProperty=nameWithType> to `JsonNamingPolicy.CamelCase`, as shown in the following example:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/RoundTripCamelCasePropertyNames.cs" id="Serialize":::

<span data-ttu-id="15233-122">下面是要进行序列化的示例类和 JSON 输出：</span><span class="sxs-lookup"><span data-stu-id="15233-122">Here's an example class to serialize and JSON output:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/WeatherForecast.cs" id="WFWithPropertyNameAttribute":::

```json
{
  "date": "2019-08-01T00:00:00-07:00",
  "temperatureCelsius": 25,
  "summary": "Hot",
  "Wind": 35
}
```

<span data-ttu-id="15233-123">camel 大小写属性命名策略：</span><span class="sxs-lookup"><span data-stu-id="15233-123">The camel case property naming policy:</span></span>

* <span data-ttu-id="15233-124">适用于序列化和反序列化。</span><span class="sxs-lookup"><span data-stu-id="15233-124">Applies to serialization and deserialization.</span></span>
* <span data-ttu-id="15233-125">由 `[JsonPropertyName]` 特性替代。</span><span class="sxs-lookup"><span data-stu-id="15233-125">Is overridden by `[JsonPropertyName]` attributes.</span></span> <span data-ttu-id="15233-126">这便是示例中的 JSON 属性名称 `Wind` 不是 camel 大小写的原因。</span><span class="sxs-lookup"><span data-stu-id="15233-126">This is why the JSON property name `Wind` in the example is not camel case.</span></span>

## <a name="use-a-custom-json-property-naming-policy"></a><span data-ttu-id="15233-127">使用自定义 JSON 属性命名策略</span><span class="sxs-lookup"><span data-stu-id="15233-127">Use a custom JSON property naming policy</span></span>

<span data-ttu-id="15233-128">若要使用自定义 JSON 属性命名策略，请创建派生自 <xref:System.Text.Json.JsonNamingPolicy> 的类，并替代 <xref:System.Text.Json.JsonNamingPolicy.ConvertName%2A> 方法，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="15233-128">To use a custom JSON property naming policy, create a class that derives from <xref:System.Text.Json.JsonNamingPolicy> and override the <xref:System.Text.Json.JsonNamingPolicy.ConvertName%2A> method, as shown in the following example:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/UpperCaseNamingPolicy.cs":::

<span data-ttu-id="15233-129">然后，将 <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy?displayProperty=nameWithType> 属性设置为命名策略类的实例：</span><span class="sxs-lookup"><span data-stu-id="15233-129">Then set the <xref:System.Text.Json.JsonSerializerOptions.PropertyNamingPolicy?displayProperty=nameWithType> property to an instance of your naming policy class:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/RoundtripPropertyNamingPolicy.cs" id="Serialize":::

<span data-ttu-id="15233-130">下面是要进行序列化的示例类和 JSON 输出：</span><span class="sxs-lookup"><span data-stu-id="15233-130">Here's an example class to serialize and JSON output:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/WeatherForecast.cs" id="WFWithPropertyNameAttribute":::

```json
{
  "DATE": "2019-08-01T00:00:00-07:00",
  "TEMPERATURECELSIUS": 25,
  "SUMMARY": "Hot",
  "Wind": 35
}
```

<span data-ttu-id="15233-131">JSON 属性命名策略：</span><span class="sxs-lookup"><span data-stu-id="15233-131">The JSON property naming policy:</span></span>

* <span data-ttu-id="15233-132">适用于序列化和反序列化。</span><span class="sxs-lookup"><span data-stu-id="15233-132">Applies to serialization and deserialization.</span></span>
* <span data-ttu-id="15233-133">由 `[JsonPropertyName]` 特性替代。</span><span class="sxs-lookup"><span data-stu-id="15233-133">Is overridden by `[JsonPropertyName]` attributes.</span></span> <span data-ttu-id="15233-134">这便是示例中的 JSON 属性名称 `Wind` 不是大写的原因。</span><span class="sxs-lookup"><span data-stu-id="15233-134">This is why the JSON property name `Wind` in the example is not upper case.</span></span>

## <a name="camel-case-dictionary-keys"></a><span data-ttu-id="15233-135">Camel 大小写字典键</span><span class="sxs-lookup"><span data-stu-id="15233-135">Camel case dictionary keys</span></span>

<span data-ttu-id="15233-136">如果要序列化的对象的属性为 `Dictionary<string,TValue>` 类型，则 `string` 键可转换为 camel 大小写。</span><span class="sxs-lookup"><span data-stu-id="15233-136">If a property of an object to be serialized is of type `Dictionary<string,TValue>`, the `string` keys can be converted to camel case.</span></span> <span data-ttu-id="15233-137">为此，请将 <xref:System.Text.Json.JsonSerializerOptions.DictionaryKeyPolicy> 设置为 `JsonNamingPolicy.CamelCase`，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="15233-137">To do that, set <xref:System.Text.Json.JsonSerializerOptions.DictionaryKeyPolicy> to `JsonNamingPolicy.CamelCase`, as shown in the following example:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/SerializeCamelCaseDictionaryKeys.cs" id="Serialize":::

<span data-ttu-id="15233-138">使用名为 `TemperatureRanges` 且具有键值对 `"ColdMinTemp", 20` 和 `"HotMinTemp", 40` 的字典序列化对象会产生类似于以下示例的 JSON 输出：</span><span class="sxs-lookup"><span data-stu-id="15233-138">Serializing an object with a dictionary named `TemperatureRanges` that has key-value pairs `"ColdMinTemp", 20` and `"HotMinTemp", 40` would result in JSON output like the following example:</span></span>

```json
{
  "Date": "2019-08-01T00:00:00-07:00",
  "TemperatureCelsius": 25,
  "Summary": "Hot",
  "TemperatureRanges": {
    "coldMinTemp": 20,
    "hotMinTemp": 40
  }
}
```

<span data-ttu-id="15233-139">字典键的 camel 大小写命名策略仅适用于序列化。</span><span class="sxs-lookup"><span data-stu-id="15233-139">The camel case naming policy for dictionary keys applies to serialization only.</span></span> <span data-ttu-id="15233-140">如果对字典进行反序列化，即使为 `DictionaryKeyPolicy`指定 `JsonNamingPolicy.CamelCase`，键也会与 JSON 文件匹配。</span><span class="sxs-lookup"><span data-stu-id="15233-140">If you deserialize a dictionary, the keys will match the JSON file even if you specify `JsonNamingPolicy.CamelCase` for the `DictionaryKeyPolicy`.</span></span>

## <a name="enums-as-strings"></a><span data-ttu-id="15233-141">作为字符串的枚举</span><span class="sxs-lookup"><span data-stu-id="15233-141">Enums as strings</span></span>

<span data-ttu-id="15233-142">默认情况下，枚举会序列化为数字。</span><span class="sxs-lookup"><span data-stu-id="15233-142">By default, enums are serialized as numbers.</span></span> <span data-ttu-id="15233-143">若要将枚举名称序列化为字符串，请使用 <xref:System.Text.Json.Serialization.JsonStringEnumConverter>。</span><span class="sxs-lookup"><span data-stu-id="15233-143">To serialize enum names as strings, use the <xref:System.Text.Json.Serialization.JsonStringEnumConverter>.</span></span>

<span data-ttu-id="15233-144">例如，假设需要序列化以下具有枚举的类：</span><span class="sxs-lookup"><span data-stu-id="15233-144">For example, suppose you need to serialize the following class that has an enum:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/WeatherForecast.cs" id="WFWithEnum":::

<span data-ttu-id="15233-145">如果 Summary 为 `Hot`，则默认情况下序列化的 JSON 具有数值 3：</span><span class="sxs-lookup"><span data-stu-id="15233-145">If the Summary is `Hot`, by default the serialized JSON has the numeric value 3:</span></span>

```json
{
  "Date": "2019-08-01T00:00:00-07:00",
  "TemperatureCelsius": 25,
  "Summary": 3
}
```

<span data-ttu-id="15233-146">下面的示例代码序列化枚举名称(而不是数值)，并将名称转换为 camel 大小写：</span><span class="sxs-lookup"><span data-stu-id="15233-146">The following sample code serializes the enum names instead of the numeric values, and converts the names to camel case:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/RoundtripEnumAsString.cs" id="Serialize":::

<span data-ttu-id="15233-147">生成的 JSON 类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="15233-147">The resulting JSON looks like the following example:</span></span>

```json
{
  "Date": "2019-08-01T00:00:00-07:00",
  "TemperatureCelsius": 25,
  "Summary": "hot"
}
```

<span data-ttu-id="15233-148">还可以反序列化枚举字符串名称，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="15233-148">Enum string names can be deserialized as well, as shown in the following example:</span></span>

:::code language="csharp" source="snippets/system-text-json-how-to/csharp/RoundtripEnumAsString.cs" id="Deserialize":::

## <a name="see-also"></a><span data-ttu-id="15233-149">请参阅</span><span class="sxs-lookup"><span data-stu-id="15233-149">See also</span></span>

* [<span data-ttu-id="15233-150">System.Text.Json 概述</span><span class="sxs-lookup"><span data-stu-id="15233-150">System.Text.Json overview</span></span>](system-text-json-overview.md)
* [<span data-ttu-id="15233-151">实例化 JsonSerializerOptions</span><span class="sxs-lookup"><span data-stu-id="15233-151">Instantiate JsonSerializerOptions</span></span>](system-text-json-configure-options.md)
* [<span data-ttu-id="15233-152">启用不区分大小写的匹配</span><span class="sxs-lookup"><span data-stu-id="15233-152">Enable case-insensitive matching</span></span>](system-text-json-character-casing.md)
* [<span data-ttu-id="15233-153">忽略属性</span><span class="sxs-lookup"><span data-stu-id="15233-153">Ignore properties</span></span>](system-text-json-ignore-properties.md)
* [<span data-ttu-id="15233-154">允许无效的 JSON</span><span class="sxs-lookup"><span data-stu-id="15233-154">Allow invalid JSON</span></span>](system-text-json-invalid-json.md)
* [<span data-ttu-id="15233-155">处理溢出 JSON</span><span class="sxs-lookup"><span data-stu-id="15233-155">Handle overflow JSON</span></span>](system-text-json-handle-overflow.md)
* [<span data-ttu-id="15233-156">保留循环引用</span><span class="sxs-lookup"><span data-stu-id="15233-156">Preserve circular references</span></span>](system-text-json-preserve-references.md)
* [<span data-ttu-id="15233-157">不可变类型和非公共访问器</span><span class="sxs-lookup"><span data-stu-id="15233-157">Immutable types and non-public accessors</span></span>](system-text-json-immutability.md)
* [<span data-ttu-id="15233-158">多态序列化</span><span class="sxs-lookup"><span data-stu-id="15233-158">Polymorphic serialization</span></span>](system-text-json-polymorphism.md)
* <span data-ttu-id="15233-159">[System.Text.Json API 参考](xref:System.Text.Json)</span><span class="sxs-lookup"><span data-stu-id="15233-159">[System.Text.Json API reference](xref:System.Text.Json)</span></span>