---
title: 适用于 .NET 的 SOS 调试扩展
description: 了解适用于 .NET 的 SOS 调试扩展，该扩展提供有关内部 CLR 环境的信息。
ms.date: 12/21/2020
helpviewer_keywords:
- debugging extensions
- SOS debugging extensions
- SOS.dll
ms.openlocfilehash: a24b946c4ffda59e17f61c065d2846dc1a6effe0
ms.sourcegitcommit: c0b803bffaf101e12f071faf94ca21b46d04ff30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/24/2020
ms.locfileid: "97764963"
---
# <a name="sos-debugging-extension"></a>SOS 调试扩展

SOS 调试扩展让你可查看有关在在 .NET Core 运行时（包括实时进程和转储）内运行的代码的信息。 该扩展已预安装 [dotnet-dump](dotnet-dump.md) 和 [Windbg/dbg](/windows-hardware/drivers/debugger/debugger-download-tools)，并可供[下载](dotnet-sos.md)，以便与 LLDB 配合使用。  SOS 调试扩展可用于收集有关托管堆的信息、查找堆损坏情况、显示运行时所使用的内部数据类型以及查看有关在运行时内运行的所有托管代码的信息。

## <a name="syntax"></a>语法

### <a name="windows"></a>Windows

`![command] [options]`

### <a name="linux-and-macos"></a>Linux 和 macOS

`sos [command] [options]`

许多命令在 lldb 下都有别名或快捷方式：

`clrstack [options]`

## <a name="commands"></a>命令

下表的命令还可在 Help 或 soshelp 下使用 。  使用 `soshelp <command>` 可提供单个命令帮助。

|Command|说明|
|-------------|-----------------|
|**bpmd** [ **-nofuturemodule**] [\<*module name*> \<*method name*>] [ **-md** <`MethodDesc`>] **-list** **-clear** \<*pending breakpoint number*> **-clearall**|在指定模块中的指定方法处创建断点。<br /><br /> 如果尚未加载指定的模块和方法，则此命令将在创建断点之前等待已加载并进行实时 (JIT) 编译的模块的通知。<br /><br /> 可以通过使用 **-list**、 **-clear** 和 **-clearall** 选项来管理挂起断点的列表：<br /><br /> 该 **-list** 选项生成所有挂起断点的列表。 如果挂起断点有一个非零模块 ID，则该断点特定于该特定已加载模块中的函数。 如果挂起断点有一个零模块 ID，则该断点适用于尚未加载的模块。<br /><br /> 使用 **-clear** 或 **-clearall** 选项可从该列表中移除挂起断点。|
|**CLRStack** [ **-a**] [ **-l**] [ **-p**] [ **-n**] [ **-f**] [ **-r**] [ **-all**]|仅提供托管代码的堆栈跟踪。<br /><br /> **-p** 选项显示托管函数的自变量。<br /><br /> **-l** 选项显示有关帧中的局部变量的信息。 SOS 调试扩展无法检索本地名称，因此本地名称的输出采用的格式为 \<*local address*> **=** \<*value*>。<br /><br /> “-a”选项是 -l 和 -p 组合的快捷方式  。<br /><br /> **-n** 选项禁止显示源文件名和行号。 如果调试器已指定选项 SYMOPT_LOAD_LINES，则 SOS 将查找每个托管帧的符号，如果成功，则将显示对应的源文件名和行号。 可以指定 **-n**（无行号）参数来禁用此行为。<br /><br />“-f”选项（完整模式）显示原生帧，将其与托管帧混合在一起，并显示托管帧的程序集名称和函数偏移量。  与 `dotnet-dump` 一起使用时，此选项不显示原生帧。<br /><br />“-r”选项转储每个堆栈帧的寄存器。<br /><br />“-all”选项转储所有托管线程的堆栈。|
|**COMState**|列出每个线程的 COM 单元模型和 `Context` 指针（如果可用）。 此命令仅可用于 Windows。|
|**DumpArray** [ **-start** \<*startIndex*>] [ **-length** \<*length*>] [ **-details**] [ **-nofields**] \<*array object address*><br /><br /> \- 或 -<br /><br /> **DA** [ **-start** \<*startIndex*>] [ **-length** \<*length*>] [ **-detail**] [ **-nofields**] *array object address*>|检查数组对象的元素。<br /><br /> **-start** 选项指定开始显示元素的起始索引。<br /><br /> **-length** 选项指定要显示的元素数量。<br /><br /> **-details** 选项使用 **DumpObj** 和 **DumpVC** 格式显示元素的详细信息。<br /><br /> **-nofields** 选项可阻止显示数组。 此选项仅在指定 **-detail** 选项后可用。|
|**DumpAsync** (**dumpasync**) [ **-mt** \<*MethodTable address*>] [ **-type** \<*partial type name*>] [ **-waiting**] [ **-roots**]|DumpAsync 遍历垃圾回收堆，查找表示异步状态机的对象（在将异步方法的状态传输到堆时创建）。  此命令识别定义为 `async void`、`async Task`、`async Task<T>`、`async ValueTask` 和 `async ValueTask<T>` 的异步状态机。<br /><br />输出包括找到的每个异步状态机对象的详细信息块。 这些详细信息包括：<br />- 一行为异步状态机对象的类型，其中包括其 MethodTable 地址、对象地址、大小和类型名称。<br />- 一行为对象中包含的状态机类型名称。<br />- 状态机上每个字段的列表。<br />- 一行为此状态机对象的延续（如果已注册一个或多个）。<br />- 为此异步状态机对象发现的 GC 根。<br />|
|**DumpAssembly** \<*assembly address*>|显示有关程序集的信息。<br /><br /> **DumpAssembly** 命令将列出多个模块（如果存在）。<br /><br /> 可以通过使用 **DumpDomain** 命令获取程序集地址。|
|**DumpClass** \<*EEClass address*>|显示有关与类型关联的 `EEClass` 结构的信息。<br /><br /> **DumpClass** 命令显示静态字段值，但不显示非静态字段值。<br /><br /> 使用 **DumpMT**、**DumpObj**、**Name2EE** 或 **Token2EE** 命令获取 `EEClass` 结构地址。|
|**DumpDomain** [\<*domain address*>]|枚举在指定的 <xref:System.Reflection.Assembly> 对象地址内加载的每个 <xref:System.AppDomain> 对象。  若在调用 DumpDomain 命令时不提供任何参数，则将列出过程中的所有 <xref:System.AppDomain> 对象。 由于 .NET Core 仅有一个 <xref:System.AppDomain>，因此 DumpDomain 将仅返回一个对象。|
|**DumpHeap** [ **-stat**] [ **-strings**] [ **-short**] [ **-min** \<*size*>] [ **-max** \<*size*>] [ **-thinlock**] [ **-startAtLowerBound**] [ **-mt** \<*MethodTable address*>] [ **-type** \<*partial type name*>][*start* [*end*]]|显示有关垃圾回收堆的信息和有关对象的收集统计信息。<br /><br /> 如果 **DumpHeap** 命令在垃圾回收器堆中检测到过多碎片，将会显示警告。<br /><br /> **-stat** 选项将输出限制为统计类型摘要。<br /><br /> **-strings** 选项将输出限制为统计字符串值摘要。<br /><br /> **-short** 选项将输出限制为只是每个对象的地址。 这使你能够轻松地以管道方式将输出从该命令转移到另一个调试器命令以实现自动化。<br /><br /> **-min** 选项忽略小于 `size` 参数指定的大小（以字节为单位）的对象。<br /><br /> **-max** 选项忽略大于 `size` 参数指定的大小（以字节为单位）的对象。<br /><br /> **-thinlock** 选项报告 ThinLocks。  有关详细信息，请参阅 **SyncBlk** 命令。<br /><br /> `-startAtLowerBound` 选项将强制在提供的地址范围的下限开始堆审核。 在计划阶段，堆通常不可移动，因为对象正在移动。 此选项将强制 **DumpHeap** 在指定的下限开始其审核。 你必须提供有效的对象的地址作为使此选项工作的下限。 你可以显示错误对象的地址的内存来手动寻找下一个方法表。 如果垃圾回收当前处于对 `memcopy` 的调用中，则你还可以通过将大小添加到作为参数提供的起始地址中来查找下一个对象的地址。<br /><br /> **-mt** 选项仅列出与指定的 `MethodTable` 结构对应的那些对象。<br /><br /> **-type** 选项仅列出其类型名称为指定字符串的子字符串匹配项的那些对象。<br /><br /> `start` 参数从指定的地址处开始列出。<br /><br /> `end` 参数在指定的地址处停止列出。|
|**DumpIL** \<*Managed DynamicMethod object*> &#124;       \<*DynamicMethodDesc pointer*> &#124;        \<*MethodDesc pointer*>|显示与托管方法关联的 Microsoft 中间语言 (MSIL)。<br /><br /> 发出的动态 MSIL 与从程序集加载的 MSIL 不同。 动态 MSIL 引用托管对象数组中的对象而不是引用元数据标记。|
|**DumpLog** [ **-addr** \<*addressOfStressLog*>] [<*Filename*>]|将内存中压力日志的内容写入到指定文件。 如果你不指定名称，则此命令将在当前目录中创建一个名为 StressLog.txt 的文件。<br /><br /> 内存中压力日志可帮助你在不使用锁或 I/O 的情况下诊断压力故障。 若要启用压力日志，请在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework 下设置以下注册表项：<br /><br /> (DWORD) StressLog = 1<br /><br /> (DWORD) LogFacility = 0xffffffff<br /><br /> (DWORD) StressLogSize = 65536<br /><br /> 利用可选的 `-addr` 选项，你可以指定压力日志而非默认日志。|
|**DumpMD** \<*MethodDesc address*>|显示有关指定地址处的 `MethodDesc` 结构的信息。<br /><br /> 可以使用 **IP2MD** 命令从托管函数中获取 `MethodDesc` 结构地址。|
|**DumpMT** [ **-MD**] \<*MethodTable address*>|显示有关指定地址处的方法表的信息。 指定 **-MD** 选项将显示与对象一起定义的所有方法的列表。<br /><br /> 每个托管对象均包含一个方法表指针。|
|**DumpModule** [ **-mt**] \<*Module address*>|显示有关指定地址处的模块的信息。 **-mt** 选项显示模块中定义的类型和模块所引用的类型<br /><br /> 可以使用 **DumpDomain** 或 **DumpAssembly** 命令检索模块的地址。|
|**DumpObj** [ **-nofields**] \<*object address*><br /><br /> \- 或 -<br /><br /> **DO** \<*object address*>|显示有关指定地址处的对象的信息。 **DumpObj** 命令显示对象的字段、`EEClass` 结构信息、方法表和大小。<br /><br /> 可以使用 **DumpStackObjects** 命令检索对象的地址。<br /><br /> 可以对 `CLASS` 类型的字段运行 DumpObj 命令，因为这些字段也是对象。<br /><br /> `-`**nofields** 选项可阻止显示对象的字段，它对 String 这样的对象很有用。|
|**DumpRuntimeTypes**|显示垃圾回收器堆中的运行时类型对象并列出其关联的类型名称和方法表。|
|**DumpStack** [ **-EE**] [ **-n**] [`top` *stack* [`bottom` *stac*`k`]]|显示堆栈跟踪。<br /><br /> **-EE** 选项使 **DumpStack** 命令仅显示托管函数。 使用 `top` 和 `bottom` 参数可限制 x86 平台上显示的堆栈帧。<br /><br /> **-n** 选项禁止显示源文件名和行号。 如果调试器已指定选项 SYMOPT_LOAD_LINES，则 SOS 将查找每个托管帧的符号，如果成功，则将显示对应的源文件名和行号。 可以指定 **-n**（无行号）参数来禁用此行为。<br /><br />
|**DumpSig** \<*sigaddr*> \<*moduleaddr*>|显示有关指定地址处的 `Sig` 结构的信息。|
|**DumpSigElem** \<*sigaddr*> \<*moduleaddr*>|显示签名对象的单个元素。 大多数情况下，应使用 **DumpSig** 查看单个签名对象。 但是，如果签名已在某种程度上被损坏，则可使用 **DumpSigElem** 读取它的有效部分。|
|**DumpStackObjects** [ **-verify**] [`top` *stack* [`bottom` *stack*]]<br /><br /> \- 或 -<br /><br /> **DSO** [ **-verify**] [`top` *stack* [`bottom` *stack*]]|显示在当前堆栈的边界内找到的所有托管对象。<br /><br /> **-verify** 选项验证对象字段的每个非静态 `CLASS` 字段。<br /><br /> 将 DumpStackObject 命令与堆栈跟踪命令（如 K (windbg) 或 bt (lldb)）及 clrstack 命令一起使用，以确定局部变量和参数的值   。|
|**DumpVC** \<*MethodTable address*> \<*Address*>|显示有关指定地址处的值类字段的信息。<br /><br /> **MethodTable** 参数使 **DumpVC** 命令可以正确解释字段。 值类不将方法表作为其第一个字段。|
|**EEHeap** [ **-gc**] [ **-loader**]|显示有关内部运行时数据结构所使用的进程内存的信息。<br /><br /> **-gc** 和 **-loader** 选项将此命令的输出限制为垃圾回收器或加载程序数据结构。<br /><br /> 有关垃圾回收器的信息列出了托管堆中每个段的范围。  如果指针落在由 **-gc** 给定的段范围内，则该指针是一个对象指针。|
|**EEStack** [ **-short**] [ **-EE**]|对一个进程中的所有线程运行 **DumpStack** 命令。<br /><br /> 将 **-EE** 选项直接传递给 **DumpStack** 命令。 **-short** 参数将输出限制为以下类型的线程：<br /><br />已获取锁的线程。<br /><br />己停止运行以允许垃圾回收的线程。<br /><br /> 当前在托管代码中的线程。|
|**EHInfo** [\<*MethodDesc address*>] [\<*Code address*>]|显示指定方法中的异常处理块。  此命令显示子句块（`try` 块）和处理程序块（`catch` 块）的代码地址和偏移量。|
|**常见问题解答**|显示常见问题。  `dotnet-dump`不支持。|
|**FinalizeQueue** [ **-detail**] &#124; [ **-allReady**] [ **-short**]|显示所有已进行终结注册的对象。<br /><br /> **-detail** 选项显示有关需要清理的任何 `SyncBlocks` 的额外信息，以及有关等待清理的任何 `RuntimeCallableWrappers` (RCW) 的额外信息。 这两种数据结构都由终结器线程在运行时进行缓存和清理。<br /><br /> `-allReady` 选项显示所有准备终止的对象，无论它们已被垃圾回收标记成这样，还是将被下一个垃圾回收标记。 “准备终止”列表中的对象为不再为根的可终止对象。 此选项可能耗费大量资源，因为它验证可终止队列中的所有对象是否仍然为根对象。<br /><br /> `-short` 选项将输出限制为每个对象的地址。 如果将此选项与 **-allReady** 一起使用，则将枚举具有不再为根的终结器的所有对象。 如果单独使用此选项，则将列出终结和“准备终结”队列中的所有对象。|
|**FindAppDomain** \<*Object address*>|确定指定地址处的对象的应用程序域。|
|**FindRoots** **-gen** \<*N*> &#124; **-gen any** &#124;\<*object address*>|导致调试器在指定生成的下次回收时在调试对象中中断。 在中断发生时立即重置该效果。 若要在下一个集合中断，你必须重新发出该命令。 此命令的 *\<object address>* 形式在 -gen 或 -gen any 引起的中断发生后使用。 此时，调试对象处于正确的状态，以便 **FindRoots** 从当前已报废的生成中识别出对象的根。 只有在 Windows 上才受支持。|
|**GCHandles** [ **-perdomain**]|显示有关进程中的垃圾回收器句柄的统计信息。<br /><br /> **-perdomain** 选项将按应用程序域排列统计信息。<br /><br /> 使用 **GCHandles** 命令可查找由垃圾回收器句柄泄漏导致的内存泄漏。 例如，当代码由于强垃圾回收器句柄仍指向一个大型数组而保留该数组时，若不释放句柄就将其放弃，则会发生内存泄漏。 <br /><br />只有在 Windows 上才受支持。|
|**GCHandleLeaks**|在内存中搜索进程中的对强垃圾回收器句柄和固定垃圾回收器句柄的任何引用并显示结果。 如果找到某个句柄，**GCHandleLeaks** 命令将显示相应的引用地址。 如果在内存中找不到句柄，此命令将显示一个通知。 只有在 Windows 上才受支持。|
|**GCInfo** \<*MethodDesc address*>\<*Code address*>|显示指示寄存器或堆栈位置何时包含托管对象的数据。 如果发生垃圾回收，回收器必须知道对象引用的位置，以便可以使用新的对象指针值更新相应的对象引用。|
|**GCRoot** [ **-nostacks**] [ **-all**] \<*Object address*>|显示有关对指定地址处的对象的引用（或根）的信息。<br /><br /> **GCRoot** 命令将检查整个托管堆和句柄表以查找其他对象内的句柄和堆栈上的句柄。 然后，在每个堆栈中搜索对象的指针，同时还搜索终结器队列。<br /><br /> 此命令无法确定堆栈根是有效的还是已丢弃。 使用 clrstack 和 U 命令可对本地或自变量值所属的帧进行反汇编，以便确定堆栈根是否仍在使用中 。<br /><br /> -nostacks 选项将搜索限制为垃圾回收器句柄和 reachable 对象。<br /><br /> “-all”选项强制显示所有根，而不仅仅是唯一的根。|
|**GCWhere**  *\<object address>*|显示垃圾回收堆中自变量传入的位置和大小。 如果自变量位于托管堆中，但不是有效的对象地址，则大小显示为 0（零）。|
|**Help** (**soshelp**) [\<*command*>] [`faq`]|在未指定参数时显示所有可用命令，或者显示有关指定命令的详细帮助信息。<br /><br /> `faq` 参数显示常见问题的答案。|
|**HeapStat** [ **-inclUnrooted** &#124; **-iu**]|显示每个堆的生成大小及每个堆上各生成的可用空间总量。 如果指定 -**inclUnrooted** 选项，则报告将包括有关不再为根的垃圾回收堆中的托管对象的信息。 只有在 Windows 上才受支持。|
|**HistClear**|释放由 `Hist` 命令系列使用的任何资源。<br /><br /> 通常，你不必显式调用 `HistClear`，因为每个 `HistInit` 都会清理以前的资源。|
|**HistInit**|从保存在调试对象中的压力日志初始化 SOS 结构。|
|**HistObj** *<obj_address>*|检查所有压力日志的重定位记录，并显示可能已将地址作为自变量传入的垃圾回收重定位链。|
|**HistObjFind**  *<obj_address>*|显示在指定地址处引用对象的所有日志项。|
|**HistRoot** *\<root>*|显示与指定根的提升和重定位相关的信息。<br /><br /> 根值可用于通过垃圾回收来跟踪对象的移动。|
|**IP2MD** (**ip2md**) \<*Code address*>|显示已 JIT 编译的代码中指定地址处的 `MethodDesc` 结构。|
|**ListNearObj** (**lno**) *<obj_address>*|显示指定地址之前和之后的对象。 该命令在垃圾回收堆和自变量地址之后的对象中寻找地址，而该垃圾回收堆看上去像托管对象（基于有效的方法表）的有效开头。 只有在 Windows 上才受支持。|
|**MinidumpMode** [**0**] [**1**]|防止在使用小型转储时运行不安全的命令。<br /><br /> 传递 **0** 禁用此功能，或传递 **1** 启用此功能。 默认情况下，**MinidumpMode** 值设置为 **0**。<br /><br /> 使用 **.dump /m** 命令或 **.dump** 命令创建的小型转储具有有限的 CLR 特定数据，允许只正确地运行一小部分 SOS 命令。 有些命令可能会因错误而失败，因为所需的内存区域未被映射或仅被部分映射。 此选项可防止你对小型转储运行不安全的命令。 <br /><br />仅在 Windbg 中受支持。|
|**Name2EE** (**name2ee**) \<*module name*> \<*type or method name*><br /><br /> \- 或 -<br /><br /> **Name2EE** \<*module name*> **!** \<*type or method name*>|显示指定模块中的指定类型或方法的 `MethodTable` 结构和 `EEClass` 结构。<br /><br /> 在进程中必须加载指定的模块。<br /><br /> 若要获取正确的类型名称，请通过使用 [Ildasm.exe（IL 反汇编程序）](../../../docs/framework/tools/ildasm-exe-il-disassembler.md)浏览模块。 也可以将 `*` 作为模块名参数传递以搜索所有已加载的托管模块。 *module name* 参数也可以是模块的调试器名称，如 `mscorlib` 或 `image00400000`。<br /><br /> 此命令支持 <`module`>`!`<`type`> 的 Windows 调试器语法。 类型必须是完全限定的。|
|**ObjSize** [\<*Object address*>] &#124; [ **-aggregate**] [ **-stat**]|显示指定对象的大小。 如果未指定任何参数，则 **ObjSize** 命令将显示在托管线程上找到的所有对象的大小，显示进程中的所有垃圾回收器句柄，并对这些句柄指向的任何对象的大小进行合计。 除父对象之外，**ObjSize** 命令还包括所有子对象的大小。<br /><br /> **-aggregate** 选项能够与 **-stat** 参数一起使用，以获得仍为根类型的类型的详细视图。 通过使用 **!dumpheap -stat** 和 **!objsize -aggregate -stat**，可以确定不再为根的对象并诊断各种内存问题。 <br /><br /> 只有在 Windows 上才受支持。|
|**PrintException** [ **-nested**] [ **-lines**] [\<*Exception object address*>]<br /><br /> \- 或 -<br /><br /> **PE** [ **-nested**] [\<*Exception object address*>]|显示从指定地址处的 <xref:System.Exception> 类派生的任何对象的字段并设置这些字段的格式。 如果不指定地址，**PrintException** 命令将显示在当前线程上引发的最后一个异常。<br /><br /> **-nested** 选项显示有关嵌套异常对象的详细信息。<br /><br /> **-lines** 选项显示源信息（如果可用）。<br /><br /> 可以使用此命令设置 `_stackTrace` 字段的格式并查看该字段（它是一个二进制数组）。|
|**ProcInfo** [ **-env**] [ **-time**] [ **-mem**]|显示进程的环境变量、内核 CPU 时间和内存使用统计信息。 仅在 Windbg 中受支持。|
|**RCWCleanupList** \<*RCWCleanupList address*>|显示在指定地址处等待清理的运行时可调用包装器的列表。 仅在 Windbg 中受支持。|
|**SaveModule** \<*Base address*> \<*Filename*>|将加载到内存中指定地址的图像写入指定文件。 仅在 Windbg 中受支持。|
|**SetHostRuntime** [\<runtime-directory\>]|此命令会设置 .NET Core 运行时的路径，以用于托管在调试器 (lldb) 中作为 SOS 的一部分运行的托管代码。 运行时至少需要为 2.1.0 或更高版本。 如果目录中有空格，则需要使用单引号 (') 引起来。<br/><br/>通常，SOS 会尝试查找已安装的 .NET Core 运行时来自动运行其托管代码，但如果失败，则可用此命令。 默认情况下使用正进行调试的同一运行时 (libcoreclr)。 如果正进行调试的默认运行时无法有效运行 SOS 代码或其版本低于 2.1.0，请使用此命令。<br/><br/>如果在运行 SOS 命令时收到以下错误消息，请使用此命令将路径设置为 2.1.0 或更高版本的 .NET Core 运行时。 <br/><br/>`(lldb) clrstack`<br/>`Error: Fail to initialize CoreCLR 80004005 ClrStack failed`<br/><br/>`(lldb) sethostruntime /usr/share/dotnet/shared/Microsoft.NETCore.App/2.1.6`<br/><br/>可以在命令行界面中使用“dotnet --info”来查找已安装 .NET Core 运行时的路径。|
|**SetSymbolServer** [ **-ms**] [ **-disable**] [ **-log**] [ **-loadsymbols**] [ **-cache** \<cache-path>] [ **-directory** \<search-directory>] [ **-sympath** \<windows-symbol-path>] [\<symbol-server-URL>]|启用符号服务器下载支持。<br/><br/>“-ms”选项允许从公共 Microsoft 符号服务器下载。<br/><br/>“-disable”选项启用符号下载支持。<br/><br/>-cache \<cache-path> 选项指定符号缓存目录。 如果未指定，则默认为 $HOME/.dotnet/symbolcache。<br/><br/>“-directory”选项添加路径来搜索符号。 可以有多个。<br/><br/>“-sympath”选项以 Windows 符号路径格式添加服务器、缓存和目录路径。<br/><br/>“-log”选项启用符号下载日志记录。<br/><br/>“-loadsymbols”选项尝试下载运行时的原生 .NET Core 符号。 lldb 和 dotnet-dump 上受支持|
|**SOSFlush**|刷新内部 SOS 缓存。|
|**SOSStatus** [ **-reset**]|显示内部 SOS 状态或重置内部缓存状态。|
|**StopOnException** [ **-derived**] [ **-create** &#124; **-create2**] \<*Exception*> \<*Pseudo-register number*>|使调试器在引发指定异常时停止，但在引发其他异常时继续运行。<br /><br /> **-derived** 选项用于捕获指定异常以及从指定异常派生的每个异常。 <br /><br /> 仅在 Windbg 中受支持。|
|**SyncBlk** [ **-all** &#124; \<*syncblk number*>]|显示指定的 `SyncBlock` 结构或所有 `SyncBlock` 结构。  如果不传递任何自变量，**SyncBlk** 命令将显示与一个线程拥有的对象对应的 `SyncBlock` 结构。<br /><br /> `SyncBlock` 结构是一个容器，用于存放无需为每个对象创建的额外信息。 它可以存放 COM 互操作数据、哈希代码和用于线程安全操作的锁定信息。|
|**ThreadPool**|显示有关托管线程池的信息，包括队列中工作请求的数目、完成端口线程的数目和计时器的数目。|
|**Threads** (**clrthreads**) [ **-live**] [ **-special**]|显示进程中的所有托管线程。<br /><br /> Threads 命令显示调试程序速记 ID、CLR 线程 ID 以及操作系统线程 ID。  此外，**Threads** 命令还会显示“Domain”列、“APT”列和“Exception”列，这三列分别用于指示正在执行某线程的应用程序域、显示 COM 单元模式以及显示该线程中引发的上一个异常。<br /><br /> **-live** 选项显示与活动线程关联的线程。<br /><br />  选项显示由 CLR 创建的所有特殊线程。 特殊线程包括垃圾回收线程（在并发垃圾回收和服务器垃圾回收中）、调试器帮助程序线程、终结器线程、<xref:System.AppDomain> 卸载线程和线程池计时器线程。|
|**ThreadState \<** *State value field* **>**|显示线程的状态。 `value` 参数为 **Threads** 报告输出中的 `State` 字段的值。|
|**Token2EE** \<*module name*> \<*token*>|将指定模块中的指定元数据标记转换成 `MethodTable` 结构或 `MethodDesc` 结构。<br /><br /> 可以传递 `*` 作为模块名参数，以便在每个已加载的托管模块中查找该标记映射到的内容。 也可以传递某个模块的调试器名称，如 `mscorlib` 或 `image00400000`。|
|**U** [ **-gcinfo**] [ **-ehinfo**] [ **-n**] \<*MethodDesc address*> &#124; \<*Code address*>|显示由方法的 `MethodDesc` 结构指针或方法体内的代码地址指定的托管方法的反汇编（带有批注）。 **U** 命令将从开始到结束显示整个方法，并带有将元数据标记转换为名称的批注。<br /><br /> **-gcinfo** 选项使 **U** 命令显示方法的 `GCInfo` 结构。<br /><br /> **-ehinfo** 选项显示方法的异常信息。 也可以使用 **EHInfo** 命令获取此信息。<br /><br /> **-n** 选项禁止显示源文件名和行号。 如果调试器已指定选项 SYMOPT_LOAD_LINES，则 SOS 将查找每个托管帧的符号，如果成功，则将显示对应的源文件名和行号。 可以指定 **-n** 选项来禁用此行为。|
|**VerifyHeap**|检查垃圾回收器堆中是否有损坏迹象，并显示找到的任何错误。<br /><br /> 错误构造的平台调用可能导致堆损坏。 |
|**VerifyObj** \<*object address*>|检查作为自变量传递的对象是否有损坏迹象。 只有在 Windows 上才受支持。|
|**VMMap**|遍历虚拟地址空间并显示应用于每个区域的保护类型。 仅在 Windbg 中受支持。|
|**VMStat**|提供虚拟地址空间的摘要视图，并按应用于该内存的每种保护类型（可用、已保留、已提交、私有、已映射和映像）排序。 “TOTAL”列显示“AVERAGE”列乘以“BLK COUNT”列的结果。 仅在 Windbg 中受支持。|

### <a name="dotnet-dump"></a>Dotnet-Dump

有关 `dotnet-dump analyze` 的可用 SOS 命令的列表，请参阅 [dotnet-dump](dotnet-dump.md#analyze-sos-commands)。

### <a name="windows-debugger"></a>Windows 调试器

还可以通过将 SOS 调试扩展加载到 [WinDbg/dbg 调试程序](/windows-hardware/drivers/debugger/debugger-download-tools)中并在 Windows 调试程序中执行命令来使用此扩展。  可对实时进程或转储使用 SOS 命令。

若要将 SOS 调试扩展加载到 Windows 调试程序中，请在工具中运行以下命令：

```console
.loadby sos clr
```

WinDbg.exe 和 Visual Studio 使用与当前使用的 Mscorwks.dll 版本对应的 SOS.dll 版本。 默认情况下，应使用与当前版本的 Mscorwks.dll 匹配的 SOS.dll 版本。

若要使用在其他计算机上创建的转储文件，请确保该安装所附带的 Mscorwks.dll 文件存在于符号路径中，并加载相应的 SOS.dll 版本。

若要加载特定版本的 SOS.dll，请在 Windows 调试程序中输入以下命令：

```console
.load <full path to sos.dll>
```

### <a name="lldb-debugger"></a>LLDB 调试程序

有关针对 LLDB 配置 SOS 的说明，请参阅 [dotnet-sos](dotnet-sos.md)。 可对实时进程或转储使用 SOS 命令。

默认情况下，可以通过输入以下内容来使用所有 SOS 命令：`sos [command\_name]`。 但是，常见命令已有别名，因此你不需要 `sos` 前缀：

| 命令                               | 函数
| ------------------------------------- | ---------------------------------------------------------------------------------------------
|    `bpmd`                             | 在指定模块中的指定托管方法处创建断点。
|    `clrstack`                         | 仅提供托管代码的堆栈跟踪。
|    `clrthreads`                       | 列出正在运行的托管线程。
|    `clru`                             | 显示托管方法的批注反汇编。
|    `dso`                              | 显示在当前堆栈的边界内找到的所有托管对象。
|    `dumpasync`                        | 显示有关垃圾回收堆上异步状态机的信息。
|    `dumpclass`                        | 显示有关指定地址处的 `EEClass` 结构的信息。
|    `dumpdomain`                       | 显示所有 AppDomain 和指定域中的所有程序集的信息。
|    `dumpheap`                         | 显示有关垃圾回收堆的信息和有关对象的收集统计信息。
|    `dumpil`                           | 显示与托管方法关联的 Microsoft 中间语言 (MSIL)。
|    `dumplog`                          | 将内存中压力日志的内容写入到指定文件。
|    `dumpmd`                           | 显示有关指定地址处的 `MethodDesc` 结构的信息。
|    `dumpmodule`                       | 显示有关指定地址处的模块的信息。
|    `dumpmt`                           | 显示有关指定地址处的方法表的信息。
|    `dumpobj`                          | 显示有关指定地址处的对象的信息。
|    `dumpstack`                        | 显示原生和托管堆栈跟踪。
|    `eeheap`                           | 显示有关内部运行时数据结构所使用的进程内存的信息。
|    `eestack`                          | 对进程中的所有线程运行 `dumpstack` 命令。
|    `gcroot`                           | 显示有关对指定地址处的对象的引用（或根）的信息。
|    `histclear`                        | 释放由 Hist 命令系列使用的任何资源。
|    `histinit`                         | 从保存在调试对象中的压力日志初始化 SOS 结构。
|    `histobj`                          | 检查所有压力日志的重定位记录，并显示可能已将地址作为自变量传入的垃圾回收重定位链。
|    `histobjfind`                      | 显示在指定地址处引用对象的所有日志项。
|    `histroot`                         | 显示与指定根的提升和重定位相关的信息。
|    `ip2md`                            | 显示已 JIT 编译的代码中指定地址处的 `MethodDesc` 结构。
|    `loadsymbols`                      | 加载 .NET Core 原生模块符号。
|    `name2ee`                          | 显示指定模块中的指定类型或方法的 `MethodTable` 和 `EEClass` 结构。
|    `pe`                               | 显示从指定地址处的 <xref:System.Exception> 类派生的任何对象的字段并设置这些字段的格式。
|    `setclrpath`                       | 设置用于加载 coreclr dac/dbi 文件的路径。 `setclrpath <path>`
|    `sethostruntime`                   | 设置或显示用于在 SOS 中运行托管代码的 .NET Core 运行时目录。
|    `setsymbolserver`                  | 启用符号服务器支持。
|    `setsostid`                        | 设置当前 OS tid/thread 索引，而不是使用 lldb 提供的索引。 `setsostid <tid> <index>`
|    `sos`                              | 各种 coreclr 调试命令。 有关详细信息，请参阅“soshelp”。 `sos <command-name> <args>`
|    `soshelp`                          | 在未指定参数时显示所有可用命令，或者显示有关指定命令的详细帮助信息：`soshelp <command>`
|    `syncblk`                          | 显示 SyncBlock 持有者信息。

## <a name="windbgcdb-example-usage"></a>Windbg/cdb 示例用法

| Command  | 说明
| - | -
| `!dumparray -start 2 -length 5 -detail 00ad28d0` | 显示地址 `00ad28d0` 处的数组内容。  显示从第二个元素开始，连续显示五个元素。
| `!dumpassembly 1ca248` | 显示地址 `1ca248` 处的程序集内容。
| `!dumpheap` | 显示有关垃圾回收器堆的信息。
| `!DumpLog` | 将内存中压力日志的内容写入到当前目录中名为 StressLog.txt 的（默认）文件中。
 `!dumpmd 902f40` | 显示在地址 `MethodDesc` 处的 `902f40` 结构。
| `!dumpmodule 1caa50` | 显示有关地址 `1caa50` 处的模块的信息。
| `!DumpObj a79d40` | 显示有关地址 `a79d40` 处的对象的信息。
| `!DumpVC 0090320c 00a79d9c` | 使用地址 `00a79d9c` 处的方法表显示地址 `0090320c` 处的值类的字段。
| `!eeheap` -gc | 显示垃圾回收器所使用的进程内存。
| `!finalizequeue` | 显示所有已做好终结计划的对象。
| `!findappdomain 00a79d98` |  确定地址 `00a79d98` 处的对象的应用程序域。
| `!gcinfo 5b68dbb8` | 显示当前进程中的所有垃圾回收器句柄。
| `!name2ee unittest.exe MainClass.Main` | 显示模块 `unittest.exe` 的类 `MainClass` 中的 `Main` 方法的 `MethodTable` 和 `EEClass` 结构。
| `!token2ee unittest.exe 02000003` | 显示有关模块 `unittest.exe` 中的地址 `02000003` 处的元数据标记的信息。

## <a name="lldb-example-usage"></a>LLDB 示例用法

| Command  | 说明
| - | -
| `sos DumpArray -start 2 -length 5 -detail 00ad28d0` | 显示地址 `00ad28d0` 处的数组内容。  显示从第二个元素开始，连续显示五个元素。
| `sos DumpAssembly 1ca248` | 显示地址 `1ca248` 处的程序集内容。
| `dumpheap` | 显示有关垃圾回收器堆的信息。
| `dumplog` | 将内存中压力日志的内容写入到当前目录中名为 StressLog.txt 的（默认）文件中。
| `dumpmd 902f40` | 显示在地址 `MethodDesc` 处的 `902f40` 结构。
| `dumpmodule 1caa50` | 显示有关地址 `1caa50` 处的模块的信息。
| `dumpobj a79d40` | 显示有关地址 `a79d40` 处的对象的信息。
| `sos DumpVC 0090320c 00a79d9c` | 使用地址 `00a79d9c` 处的方法表显示地址 `0090320c` 处的值类的字段。
| `eeheap -gc` | 显示垃圾回收器所使用的进程内存。
| `sos FindAppDomain 00a79d98` | 确定地址 `00a79d98` 处的对象的应用程序域。
| `sos GCInfo 5b68dbb8` | 显示当前进程中的所有垃圾回收器句柄。
| `name2ee unittest.exe MainClass.Main` | 显示模块 `unittest.exe` 的类 `MainClass` 中的 `Main` 方法的 `MethodTable` 和 `EEClass` 结构。
| `sos Token2EE unittest.exe 02000003` | 显示有关模块 `unittest.exe` 中的地址 `02000003` 处的元数据标记的信息。
| `clrthreads` | 显示托管线程。

## <a name="see-also"></a>另请参阅

- [.NET 中的转储简介](dumps.md)
- [了解如何调试 .NET Core 中的内存泄漏](debug-memory-leak.md)
- [收集和分析内存转储博客](https://devblogs.microsoft.com/dotnet/collecting-and-analyzing-memory-dumps/)
- [转储分析工具 (dotnet-dump)](dotnet-dump.md)
- [SOS 安装工具 (dotnet-sos)](dotnet-sos.md)
- [堆分析工具 (dotnet-gcdump)](dotnet-gcdump.md)