---
title: 在 LCD 上显示文本
description: 了解如何使用 .NET IoT 库在液晶显示器上显示字符。
author: camsoper
ms.author: casoper
ms.date: 11/13/2020
ms.topic: tutorial
ms.prod: dotnet
ms.openlocfilehash: d4c3e373207e23877903491871f4d09e11000c1a
ms.sourcegitcommit: 721c3e4bdbb1ea0bb420818ec944c538fe5c513a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96591087"
---
<!--markdownlint-disable DOCSMD011 -->
# <a name="display-text-on-an-lcd"></a><span data-ttu-id="7c04f-103">在 LCD 上显示文本</span><span class="sxs-lookup"><span data-stu-id="7c04f-103">Display text on an LCD</span></span>

<span data-ttu-id="7c04f-104">LCD 字符显示适用于显示信息，而无需使用外部监视器。</span><span class="sxs-lookup"><span data-stu-id="7c04f-104">LCD character displays are useful for displaying information without the need for an external monitor.</span></span> <span data-ttu-id="7c04f-105">常见 LCD 字符显示可以直接连接到 GPIO pin，但这种方法要求使用最多10个 GPIO pin。</span><span class="sxs-lookup"><span data-stu-id="7c04f-105">Common LCD character displays can be connected directly to the GPIO pins, but such an approach requires the use of up to 10 GPIO pins.</span></span> <span data-ttu-id="7c04f-106">对于需要连接到设备组合的方案，将花费的许多 GPIO 标头的操作通常是不切实际的。</span><span class="sxs-lookup"><span data-stu-id="7c04f-106">For scenarios that require connecting to a combination of devices, devoting so much of the GPIO header to a character display is often impractical.</span></span>

<span data-ttu-id="7c04f-107">许多制造商通过集成的 GPIO 扩展器销售 20x4 LCD 字符显示。</span><span class="sxs-lookup"><span data-stu-id="7c04f-107">Many manufacturers sell 20x4 LCD character displays with an integrated GPIO expander.</span></span> <span data-ttu-id="7c04f-108">字符显示直接连接到 GPIO 扩展器，然后通过 Inter-Integrated 线路 (I2C) 串行协议连接到 Raspberry Pi。</span><span class="sxs-lookup"><span data-stu-id="7c04f-108">The character display connects directly to the GPIO expander, which then connects to the Raspberry Pi via the Inter-Integrated Circuit (I2C) serial protocol.</span></span>

<span data-ttu-id="7c04f-109">在本主题中，你将使用 "使用 I2C GPIO" 扩展器通过 .NET 显示 LCD 字符显示的文本。</span><span class="sxs-lookup"><span data-stu-id="7c04f-109">In this topic, you will use .NET to display text on an LCD character display using an I2C GPIO expander.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7c04f-110">先决条件</span><span class="sxs-lookup"><span data-stu-id="7c04f-110">Prerequisites</span></span>

- [!INCLUDE [prereq-rpi](../includes/prereq-rpi.md)]
- <span data-ttu-id="7c04f-111">[带有 I2C 接口的 20X4 LCD 字符显示](https://www.bing.com/images/search?q=20x4+lcd+display+with+i2c)<span class="docon docon-navigate-external x-hidden-focus"></span></span><span class="sxs-lookup"><span data-stu-id="7c04f-111">[20x4 LCD Character Display with I2C interface](https://www.bing.com/images/search?q=20x4+lcd+display+with+i2c) <span class="docon docon-navigate-external x-hidden-focus"></span></span></span>
- <span data-ttu-id="7c04f-112">跳线</span><span class="sxs-lookup"><span data-stu-id="7c04f-112">Jumper wires</span></span>
- <span data-ttu-id="7c04f-113">试验板 (可选/推荐) </span><span class="sxs-lookup"><span data-stu-id="7c04f-113">Breadboard (optional/recommended)</span></span>
- <span data-ttu-id="7c04f-114">Raspberry Pi GPIO (可选/推荐的) </span><span class="sxs-lookup"><span data-stu-id="7c04f-114">Raspberry Pi GPIO breakout board (optional/recommended)</span></span>
- [!INCLUDE [tutorial-prereq-dotnet](../includes/tutorial-prereq-dotnet.md)]

> [!NOTE]
> <span data-ttu-id="7c04f-115">LCD 字符显示有许多制造商。</span><span class="sxs-lookup"><span data-stu-id="7c04f-115">There are many manufacturers of LCD character displays.</span></span> <span data-ttu-id="7c04f-116">大多数设计都是相同的，并且制造商不会对功能产生任何影响。</span><span class="sxs-lookup"><span data-stu-id="7c04f-116">Most designs are identical, and the manufacturer shouldn't make any difference to the functionality.</span></span> <span data-ttu-id="7c04f-117">作为参考，本教程是通过 [SUNFOUNDER LCD2004](https://www.sunfounder.com/lcd2004-module.html)开发的 <span class="docon docon-navigate-external x-hidden-focus"></span> 。</span><span class="sxs-lookup"><span data-stu-id="7c04f-117">For reference, this tutorial was developed with the [SunFounder LCD2004](https://www.sunfounder.com/lcd2004-module.html) <span class="docon docon-navigate-external x-hidden-focus"></span>.</span></span>

[!INCLUDE [prepare-pi-i2c](../includes/prepare-pi-i2c.md)]

## <a name="prepare-the-hardware"></a><span data-ttu-id="7c04f-118">准备硬件</span><span class="sxs-lookup"><span data-stu-id="7c04f-118">Prepare the hardware</span></span>

<span data-ttu-id="7c04f-119">使用跳线线路将 I2C GPIO 扩展器上的四个 pin 连接到 Raspberry Pi，如下所示：</span><span class="sxs-lookup"><span data-stu-id="7c04f-119">Use jumper wires to connect the four pins on the I2C GPIO expander to the Raspberry Pi as follows:</span></span>

- <span data-ttu-id="7c04f-120">GND 到地面</span><span class="sxs-lookup"><span data-stu-id="7c04f-120">GND to ground</span></span>
- <span data-ttu-id="7c04f-121">与5V 的 VCC</span><span class="sxs-lookup"><span data-stu-id="7c04f-121">VCC to 5V</span></span>
- <span data-ttu-id="7c04f-122">SDA to SDA (GPIO 2) </span><span class="sxs-lookup"><span data-stu-id="7c04f-122">SDA to SDA (GPIO 2)</span></span>
- <span data-ttu-id="7c04f-123">在 GPIO 3) SCL 到 SCL (</span><span class="sxs-lookup"><span data-stu-id="7c04f-123">SCL to SCL (GPIO 3)</span></span>

<span data-ttu-id="7c04f-124">根据需要，请参阅以下数字：</span><span class="sxs-lookup"><span data-stu-id="7c04f-124">Refer to the following figures as needed:</span></span>

| <span data-ttu-id="7c04f-125">I2C 接口 (显示的背面) </span><span class="sxs-lookup"><span data-stu-id="7c04f-125">I2C interface (back of display)</span></span> | <span data-ttu-id="7c04f-126">Raspberry Pi GPIO</span><span class="sxs-lookup"><span data-stu-id="7c04f-126">Raspberry Pi GPIO</span></span> |
|---------------------------------|-------------------|
| :::image type="content" source="../media/character-display-i2c-thumb.png" alt-text="显示 I2C GPIO 扩展器的字符显示的背景图像。" lightbox="../media/character-display-i2c.png"::: | :::image type="content" source="../media/gpio-pinout-diagram-thumb.png" alt-text="显示 Raspberry Pi GPIO 标头引线的关系图。Image 礼节性 Raspberry Pi Foundation。" lightbox="../media/gpio-pinout-diagram.png":::<br /><span data-ttu-id="7c04f-129">[Image 礼节性 Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/usage/gpio/) <span class="docon docon-navigate-external x-hidden-focus"></span> 。</span><span class="sxs-lookup"><span data-stu-id="7c04f-129">[Image courtesy Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/usage/gpio/) <span class="docon docon-navigate-external x-hidden-focus"></span>.</span></span>
 |

[!INCLUDE [gpio-breakout](../includes/gpio-breakout.md)]

## <a name="create-the-app"></a><span data-ttu-id="7c04f-130">创建应用</span><span class="sxs-lookup"><span data-stu-id="7c04f-130">Create the app</span></span>

<span data-ttu-id="7c04f-131">在首选开发环境中完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="7c04f-131">Complete the following steps in your preferred development environment:</span></span>

1. <span data-ttu-id="7c04f-132">使用 [.NET CLI](../../core/tools/dotnet-new.md) 或 [Visual Studio](../../core/tutorials/with-visual-studio.md)创建一个新的 .net 控制台应用程序。</span><span class="sxs-lookup"><span data-stu-id="7c04f-132">Create a new .NET Console App using either the [.NET CLI](../../core/tools/dotnet-new.md) or [Visual Studio](../../core/tutorials/with-visual-studio.md).</span></span> <span data-ttu-id="7c04f-133">将其命名为 *LcdTutorial*。</span><span class="sxs-lookup"><span data-stu-id="7c04f-133">Name it *LcdTutorial*.</span></span>

    ```dotnetcli
    dotnet new console -o LcdTutorial
    ```

1. [!INCLUDE [tutorial-add-packages](../includes/tutorial-add-packages.md)]
1. <span data-ttu-id="7c04f-134">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="7c04f-134">Replace the contents of *Program.cs* with the following code:</span></span>

    :::code language="csharp" source="~/iot-samples/tutorials/LcdTutorial/Program.cs" :::

    <span data-ttu-id="7c04f-135">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="7c04f-135">In the preceding code:</span></span>

    - <span data-ttu-id="7c04f-136">[使用声明](../../csharp/whats-new/csharp-8.md#using-declarations) `I2cDevice` 通过调用 `I2cDevice.Create` 并传入 `I2cConnectionSettings` 具有和参数的新来 `busId` 创建的实例 `deviceAddress` 。</span><span class="sxs-lookup"><span data-stu-id="7c04f-136">A [using declaration](../../csharp/whats-new/csharp-8.md#using-declarations) creates an instance of `I2cDevice` by calling `I2cDevice.Create` and passing in a new `I2cConnectionSettings` with the `busId` and `deviceAddress` parameters.</span></span> <span data-ttu-id="7c04f-137">这 `I2cDevice` 表示 I2C 总线。</span><span class="sxs-lookup"><span data-stu-id="7c04f-137">This `I2cDevice` represents the I2C bus.</span></span> <span data-ttu-id="7c04f-138">`using`声明可确保释放对象并正确释放硬件资源。</span><span class="sxs-lookup"><span data-stu-id="7c04f-138">The `using` declaration ensures the object is disposed and hardware resources are released properly.</span></span>

        > [!WARNING]
        > <span data-ttu-id="7c04f-139">GPIO 扩展器的设备地址取决于制造商使用的芯片。</span><span class="sxs-lookup"><span data-stu-id="7c04f-139">The device address for the GPIO expander depends on the chip used by the manufacturer.</span></span> <span data-ttu-id="7c04f-140">使用 PCF8574A 芯片的 GPIO 扩展器提供了 PCF8574 `0x27` `0x3F` 。</span><span class="sxs-lookup"><span data-stu-id="7c04f-140">GPIO expanders equipped with a PCF8574 use the address `0x27`, while those using PCF8574A chips use `0x3F`.</span></span> <span data-ttu-id="7c04f-141">请查阅您的 LCD 文档。</span><span class="sxs-lookup"><span data-stu-id="7c04f-141">Consult your LCD's documentation.</span></span>

    - <span data-ttu-id="7c04f-142">其他 `using` 声明将创建的实例 `Pcf8574` ，并将传递 `I2cDevice` 到构造函数中。</span><span class="sxs-lookup"><span data-stu-id="7c04f-142">Another `using` declaration creates an instance of `Pcf8574` and passes the `I2cDevice` into the constructor.</span></span> <span data-ttu-id="7c04f-143">此实例表示 GPIO 扩展器。</span><span class="sxs-lookup"><span data-stu-id="7c04f-143">This instance represents the GPIO expander.</span></span>
    - <span data-ttu-id="7c04f-144">另一个 `using` 声明创建了一个 `Lcd2004` 用于表示显示的实例。</span><span class="sxs-lookup"><span data-stu-id="7c04f-144">Another `using` declaration creates an instance of `Lcd2004` to represent the display.</span></span> <span data-ttu-id="7c04f-145">向构造函数传递几个参数，描述用于与 GPIO 扩展器进行通信的设置。</span><span class="sxs-lookup"><span data-stu-id="7c04f-145">Several parameters are passed to the constructor describing the settings to use to communicate with the GPIO expander.</span></span> <span data-ttu-id="7c04f-146">GPIO 扩展器作为 `controller` 参数传递。</span><span class="sxs-lookup"><span data-stu-id="7c04f-146">The GPIO expander is passed as the `controller` parameter.</span></span>
    - <span data-ttu-id="7c04f-147">`while`循环会无限期运行。</span><span class="sxs-lookup"><span data-stu-id="7c04f-147">A `while` loop runs indefinitely.</span></span> <span data-ttu-id="7c04f-148">每次迭代：</span><span class="sxs-lookup"><span data-stu-id="7c04f-148">Each iteration:</span></span>
        1. <span data-ttu-id="7c04f-149">清除显示。</span><span class="sxs-lookup"><span data-stu-id="7c04f-149">Clears the display.</span></span>
        1. <span data-ttu-id="7c04f-150">将光标位置设置为当前行上的第一个位置。</span><span class="sxs-lookup"><span data-stu-id="7c04f-150">Sets the cursor position to the first position on the current line.</span></span>
        1. <span data-ttu-id="7c04f-151">将当前时间写入当前光标位置的显示位置。</span><span class="sxs-lookup"><span data-stu-id="7c04f-151">Writes the current time to the display at the current cursor position.</span></span>
        1. <span data-ttu-id="7c04f-152">循环访问当前行计数器。</span><span class="sxs-lookup"><span data-stu-id="7c04f-152">Iterates the current line counter.</span></span>
        1. <span data-ttu-id="7c04f-153">睡眠1000毫秒。</span><span class="sxs-lookup"><span data-stu-id="7c04f-153">Sleeps 1000 ms.</span></span>

1. [!INCLUDE [tutorial-build](../includes/tutorial-build.md)]
1. [!INCLUDE [tutorial-deploy](../includes/tutorial-deploy.md)]
1. <span data-ttu-id="7c04f-154">通过切换到部署目录并运行可执行文件，在 Raspberry Pi 上运行该应用。</span><span class="sxs-lookup"><span data-stu-id="7c04f-154">Run the app on the Raspberry Pi by switching to the deployment directory and running the executable.</span></span>

    ```bash
    ./LcdTutorial
    ```

    <span data-ttu-id="7c04f-155">观察 LCD 字符显示，因为当前时间显示在每一行上。</span><span class="sxs-lookup"><span data-stu-id="7c04f-155">Observe the LCD character display as the current time displays on each line.</span></span>

    > [!TIP]
    > <span data-ttu-id="7c04f-156">如果显示屏已亮起但看不到任何文本，请尝试调整显示屏背面的对比度。</span><span class="sxs-lookup"><span data-stu-id="7c04f-156">If the display is lit but you don't see any text, try adjusting the contrast dial on the back of the display.</span></span>

1. <span data-ttu-id="7c04f-157">通过按 <kbd>Ctrl + C</kbd>终止节目。</span><span class="sxs-lookup"><span data-stu-id="7c04f-157">Terminate the program by pressing <kbd>Ctrl+C</kbd>.</span></span>

<span data-ttu-id="7c04f-158">恭喜！</span><span class="sxs-lookup"><span data-stu-id="7c04f-158">Congratulations!</span></span> <span data-ttu-id="7c04f-159">已使用 I2C 和 GPIO 扩展器在液晶屏上显示文本！</span><span class="sxs-lookup"><span data-stu-id="7c04f-159">You've displayed text on an LCD using a I2C and a GPIO expander!</span></span>

## <a name="get-the-source-code"></a><span data-ttu-id="7c04f-160">获取源代码</span><span class="sxs-lookup"><span data-stu-id="7c04f-160">Get the source code</span></span>

<span data-ttu-id="7c04f-161">[GitHub 上提供](https://github.com/MicrosoftDocs/dotnet-iot-assets/tree/master/tutorials/LcdTutorial)了本教程的源 <span class="docon docon-navigate-external x-hidden-focus"></span> 。</span><span class="sxs-lookup"><span data-stu-id="7c04f-161">The source for this tutorial is [available on GitHub](https://github.com/MicrosoftDocs/dotnet-iot-assets/tree/master/tutorials/LcdTutorial) <span class="docon docon-navigate-external x-hidden-focus"></span>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7c04f-162">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7c04f-162">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7c04f-163">了解如何使用常规用途输入/输出使 LED 闪烁</span><span class="sxs-lookup"><span data-stu-id="7c04f-163">Learn to use General Purpose Input/Output to blink an LED</span></span>](../tutorials/blink-led.md)