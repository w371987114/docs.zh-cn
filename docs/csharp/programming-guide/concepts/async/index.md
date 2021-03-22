---
title: C# 中的异步编程
description: 对使用 async、await、Task 和 Task<T> 的异步编程的 C# 语言支持的概述
ms.date: 06/04/2020
ms.openlocfilehash: ffc2289f3b5abfe3865e1a096ee91e2e649a6427
ms.sourcegitcommit: d623f686701b94bef905ec5e93d8b55d031c5d6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2021
ms.locfileid: "103624235"
---
# <a name="asynchronous-programming-with-async-and-await"></a>使用 Async 和 Await 的异步编程

[基于任务的异步编程模型 (TAP)](task-asynchronous-programming-model.md) 提供了异步代码的抽象化。 你只需像往常一样将代码编写为一连串语句即可。 就如每条语句在下一句开始之前完成一样，你可以流畅地阅读代码。 编译器将执行许多转换，因为其中一些语句可能会开始运行并返回表示正在进行的工作的 <xref:System.Threading.Tasks.Task>。

这就是此语法的目标：支持读起来像一连串语句的代码，但会根据外部资源分配和任务完成时间以更复杂的顺序执行。 这与人们为包含异步任务的流程给予指令的方式类似。 在本文中，你将通过做早餐的指令示例来查看如何使用 `async` 和 `await` 关键字更轻松地推断包含一系列异步指令的代码。 你可能会写出与以下列表类似的指令来解释如何做早餐：

1. 倒一杯咖啡。
1. 加热平底锅，然后煎两个鸡蛋。
1. 煎三片培根。
1. 烤两片面包。
1. 在烤面包上加黄油和果酱。
1. 倒一杯橙汁。

如果你有烹饪经验，便可通过异步方式执行这些指令。 你会先开始加热平底锅以备煎蛋，接着再从培根着手。 你可将面包放进烤面包机，然后再煎鸡蛋。 在此过程的每一步，你都可以先开始一项任务，然后将注意力转移到准备进行的其他任务上。

做早餐是非并行异步工作的一个好示例。 单人（或单线程）即可处理所有这些任务。 继续讲解早餐的类比，一个人可以以异步方式做早餐，即在第一个任务完成之前开始进行下一个任务。 不管是否有人在看着，做早餐的过程都在进行。 在开始加热平底锅准备煎蛋的同时就可以开始煎了培根。 在开始煎培根后，你可以将面包放进烤面包机。

对于并行算法而言，你则需要多名厨师（或线程）。 一名厨师煎鸡蛋，一名厨师煎培根，依次类推。 每名厨师将仅专注于一项任务。 每名厨师（或线程）都在同步等待需要翻动培根或面包弹出时都将受到阻。

现在，考虑一下编写为 C# 语句的相同指令：

:::code language="csharp" source="snippets/index/AsyncBreakfast-starter/Program.cs" highlight="8-27":::

:::image type="content" source="media/synchronous-breakfast.png" alt-text="同步早餐":::

同步准备的早餐大约花费了 30 分钟，因为总耗时是每个任务耗时的总和。

> [!NOTE]
> `Coffee`、`Egg`、`Bacon`、`Toast` 和 `Juice` 类为空。 它们是仅用于演示的简单标记类，不含任何属性，且不用于其他用途。

计算机不会按人类的方式来解释这些指令。 计算机将阻塞每条语句，直到工作完成，然后再继续运行下一条语句。 这将创造出令人不满意的早餐。 后续任务直到早前任务完成后才会启动。 这样做早餐花费的时间要长得多，有些食物在上桌之前就已经凉了。

如果你希望计算机异步执行上述指令，则必须编写异步代码。

这些问题对即将编写的程序而言至关重要。 编写客户端程序时，你希望 UI 能够响应用户输入。 从 Web 下载数据时，你的应用程序不应让手机出现卡顿。 编写服务器程序时，你不希望线程受到阻塞。 这些线程可以用于处理其他请求。 存在异步替代项的情况下使用同步代码会增加你进行扩展的成本。 你需要为这些受阻线程付费。

成功的现代应用程序需要异步代码。 在没有语言支持的情况下，编写异步代码需要回调、完成事件，或其他掩盖代码原始意图的方法。 同步代码的优点是，它的分步操作使其易于扫描和理解。 传统的异步模型迫使你侧重于代码的异步性质，而不是代码的基本操作。

## <a name="dont-block-await-instead"></a>不要阻塞，而要 await

上述代码演示了不正确的实践：构造同步代码来执行异步操作。 顾名思义，此代码将阻止执行这段代码的线程执行任何其他操作。 在任何任务进行过程中，此代码也不会被中断。 就如同你将面包放进烤面包机后盯着此烤面包机一样。 你会无视任何跟你说话的人，直到面包弹出。

我们首先更新此代码，使线程在任务运行时不会阻塞。 `await` 关键字提供了一种非阻塞方式来启动任务，然后在此任务完成时继续执行。 “做早餐”代码的简单异步版本类似于以下片段：

:::code language="csharp" source="snippets/index/AsyncBreakfast-V2/Program.cs" ID="SnippetMain":::

> [!IMPORTANT]
> 总运行时间和最初同步版本大致相同。 此代码尚未利用异步编程的某些关键功能。

> [!TIP]
> `FryEggsAsync`、`FryBaconAsync` 和 `ToastBreadAsync` 的方法主体都已更新，现会分别返回 `Task<Egg>`、`Task<Bacon>` 和 `Task<Toast>`。 这些方法的名称与其原始版本不同，将包含“Async”后缀。 它们的实现在本文的稍后部分显示为[最终版本](#final-version)的一部分。

在煎鸡蛋或培根时，此代码不会阻塞。 不过，此代码也不会启动任何其他任务。 你还是会将面包放进烤面包机里，然后盯着烤面包机直到面包弹出。 但至少，你会回应任何想引起你注意的人。 在接受了多份订单的一家餐馆里，厨师可能会在做第一份早餐的同时开始制作另一份早餐。

现在，在等待任何尚未完成的已启动任务时，处理早餐的线程将不会被阻塞。 对于某些应用程序而言，此更改是必需的。 仅凭借此更改，GUI 应用程序仍然会响应用户。 然而，对于此方案而言，你需要更多的内容。 你不希望每个组件任务都按顺序执行。 最好首先启动每个组件任务，然后再等待之前任务的完成。

## <a name="start-tasks-concurrently"></a>同时启动任务

在许多方案中，你希望立即启动若干独立的任务。 然后，在每个任务完成时，你可以继续进行已准备的其他工作。 在早餐类比中，这就是更快完成做早餐的方法。 你也几乎将在同一时间完成所有工作。 你将吃到一顿热气腾腾的早餐。

<xref:System.Threading.Tasks.Task?displayProperty=nameWithType> 和相关类型是可以用于推断正在进行中的任务的类。 这使你能够编写更类似于实际做早餐方式的代码。 你可以同时开始煎鸡蛋、培根和烤面包。 由于每个任务都需要操作，所以你会将注意力转移到那个任务上，进行下一个操作，然后等待其他需要你注意的事情。

启动一项任务并等待表示运行的 <xref:System.Threading.Tasks.Task> 对象。 你将首先 `await` 每项任务，然后再处理它的结果。

让我们对早餐代码进行这些更改。 第一步是存储任务以便在这些任务启动时进行操作，而不是等待：

```csharp
Coffee cup = PourCoffee();
Console.WriteLine("coffee is ready");

Task<Egg> eggsTask = FryEggsAsync(2);
Egg eggs = await eggsTask;
Console.WriteLine("eggs are ready");

Task<Bacon> baconTask = FryBaconAsync(3);
Bacon bacon = await baconTask;
Console.WriteLine("bacon is ready");

Task<Toast> toastTask = ToastBreadAsync(2);
Toast toast = await toastTask;
ApplyButter(toast);
ApplyJam(toast);
Console.WriteLine("toast is ready");

Juice oj = PourOJ();
Console.WriteLine("oj is ready");
Console.WriteLine("Breakfast is ready!");
```

接下来，可以在提供早餐之前将用于处理培根和鸡蛋的 `await` 语句移动到此方法的末尾：

```csharp
Coffee cup = PourCoffee();
Console.WriteLine("coffee is ready");

Task<Egg> eggsTask = FryEggsAsync(2);
Task<Bacon> baconTask = FryBaconAsync(3);
Task<Toast> toastTask = ToastBreadAsync(2);

Toast toast = await toastTask;
ApplyButter(toast);
ApplyJam(toast);
Console.WriteLine("toast is ready");
Juice oj = PourOJ();
Console.WriteLine("oj is ready");

Egg eggs = await eggsTask;
Console.WriteLine("eggs are ready");
Bacon bacon = await baconTask;
Console.WriteLine("bacon is ready");

Console.WriteLine("Breakfast is ready!");
```

:::image type="content" source="media/asynchronous-breakfast.png" alt-text="异步早餐":::

异步准备的早餐大约花费了 20 分钟，由于一些任务并发运行，因此节约了时间。

上述代码效果更好。 你可以一次启动所有的异步任务。 你仅在需要结果时才会等待每项任务。 上述代码可能类似于 Web 应用程序中请求各种微服务，然后将结果合并到单个页面中的代码。 你将立即发出所有请求，然后 `await` 所有这些任务并组成 Web 页面。

## <a name="composition-with-tasks"></a>与任务组合

 除了吐司外，你准备好了做早餐的所有材料。 吐司制作由异步操作（烤面包）和同步操作（添加黄油和果酱）组成。 更新此代码说明了一个重要的概念：

> [!IMPORTANT]
> 异步操作后跟同步操作的这种组合是一个异步操作。 换言之，如果操作的任何部分是异步的，整个操作就是异步的。

上述代码展示了可以使用 <xref:System.Threading.Tasks.Task> 或 <xref:System.Threading.Tasks.Task%601> 对象来保存运行中的任务。 你首先需要 `await` 每项任务，然后再使用它的结果。 下一步是创建表示其他工作组合的方式。 在提供早餐之前，你希望等待表示先烤面包再添加黄油和果酱的任务完成。 你可以使用以下代码表示此工作：

:::code language="csharp" source="snippets/index/AsyncBreakfast-V3/Program.cs" ID="SnippetComposeToastTask":::

上述方式的签名中具有 `async` 修饰符。 它会向编译器发出信号，说明此方法包含 `await` 语句；也包含异步操作。 此方法表示先烤面包，然后再添加黄油和果酱的任务。 此方法返回表示这三个操作的组合的 <xref:System.Threading.Tasks.Task%601>。 主要代码块现在变成了：

:::code language="csharp" source="snippets/index/AsyncBreakfast-V3/Program.cs" ID="SnippetMain":::

上述更改说明了使用异步代码的一项重要技术。 你可以通过将操作分离到一个返回任务的新方法中来组合任务。 可以选择等待此任务的时间。 可以同时启动其他任务。

## <a name="asynchronous-exceptions"></a>异步异常

至此，已隐式假定所有这些任务都已成功完成。 异步方法会引发异常，就像对应的同步方法一样。 对异常和错误处理的异步支持通常与异步支持追求相同的目标：你应该编写读起来像一系列同步语句的代码。 当任务无法成功完成时，它们将引发异常。 当启动的任务为 `awaited` 时，客户端代码可捕获这些异常。 例如，假设烤面包机在烤面包时着火了。 可通过修改 `ToastBreadAsync` 方法来模拟这种情况，以匹配以下代码：

```csharp
private static async Task<Toast> ToastBreadAsync(int slices)
{
    for (int slice = 0; slice < slices; slice++)
    {
        Console.WriteLine("Putting a slice of bread in the toaster");
    }
    Console.WriteLine("Start toasting...");
    await Task.Delay(2000);
    Console.WriteLine("Fire! Toast is ruined!");
    throw new InvalidOperationException("The toaster is on fire");
    await Task.Delay(1000);
    Console.WriteLine("Remove toast from toaster");

    return new Toast();
}
```

> [!NOTE]
> 在编译前面的代码时，你将收到一个关于无法访问的代码的警告。 这是故意的，因为一旦烤面包机着火，操作就不会正常进行。

执行这些更改后，运行应用程序，输出将类似于以下文本：

```console
Pouring coffee
coffee is ready
Warming the egg pan...
putting 3 slices of bacon in the pan
cooking first side of bacon...
Putting a slice of bread in the toaster
Putting a slice of bread in the toaster
Start toasting...
Fire! Toast is ruined!
flipping a slice of bacon
flipping a slice of bacon
flipping a slice of bacon
cooking the second side of bacon...
cracking 2 eggs
cooking the eggs ...
Put bacon on plate
Put eggs on plate
eggs are ready
bacon is ready
Unhandled exception. System.InvalidOperationException: The toaster is on fire
   at AsyncBreakfast.Program.ToastBreadAsync(Int32 slices) in Program.cs:line 65
   at AsyncBreakfast.Program.MakeToastWithButterAndJamAsync(Int32 number) in Program.cs:line 36
   at AsyncBreakfast.Program.Main(String[] args) in Program.cs:line 24
   at AsyncBreakfast.Program.<Main>(String[] args)
```

请注意，从烤面包机着火到发现异常，有相当多的任务要完成。 当异步运行的任务引发异常时，该任务出错。 Task 对象包含 <xref:System.Threading.Tasks.Task.Exception?displayProperty=nameWithType> 属性中引发的异常。 出错的任务在等待时引发异常。

需要理解两个重要机制：异常在出错的任务中的存储方式，以及在代码等待出错的任务时解包并重新引发异常的方式。

当异步运行的代码引发异常时，该异常存储在 `Task` 中。 <xref:System.Threading.Tasks.Task.Exception?displayProperty=nameWithType> 属性为 <xref:System.AggregateException?displayProperty=nameWithType>，因为异步工作期间可能会引发多个异常。 引发的任何异常都将添加到 <xref:System.AggregateException.InnerExceptions?displayProperty=nameWithType> 集合中。 如果该 `Exception` 属性为 NULL，则将创建一个新的 `AggregateException` 且引发的异常是该集合中的第一项。

对于出错的任务，最常见的情况是 `Exception` 属性只包含一个异常。 当代码 `awaits` 出错的任务时，将重新引发 <xref:System.AggregateException.InnerExceptions?displayProperty=nameWithType> 集合中的第一个异常。 因此，此示例的输出显示 `InvalidOperationException` 而不是 `AggregateException`。 提取第一个内部异常使得使用异步方法与使用其对应的同步方法尽可能相似。 当你的场景可能生成多个异常时，可在代码中检查 `Exception` 属性。

继续之前，在 `ToastBreadAsync` 方法中注释禁止这两行。 你不想再引起火灾：

```csharp
Console.WriteLine("Fire! Toast is ruined!");
throw new InvalidOperationException("The toaster is on fire");
```

## <a name="await-tasks-efficiently"></a>高效地等待任务

可以通过使用 `Task` 类的方法改进上述代码末尾的一系列 `await` 语句。 其中一个 API 是 <xref:System.Threading.Tasks.Task.WhenAll%2A>，它将返回一个其参数列表中的所有任务都已完成时才完成的 <xref:System.Threading.Tasks.Task>，如以下代码中所示：

```csharp
await Task.WhenAll(eggsTask, baconTask, toastTask);
Console.WriteLine("eggs are ready");
Console.WriteLine("bacon is ready");
Console.WriteLine("toast is ready");
Console.WriteLine("Breakfast is ready!");
```

另一种选择是使用 <xref:System.Threading.Tasks.Task.WhenAny%2A>，它将返回一个当其参数完成时才完成的 `Task<Task>`。 你可以等待返回的任务，了解它已经完成了。 以下代码展示了可以如何使用 <xref:System.Threading.Tasks.Task.WhenAny%2A> 等待第一个任务完成，然后再处理其结果。 处理已完成任务的结果之后，可以从传递给 `WhenAny` 的任务列表中删除此已完成的任务。

```csharp
var breakfastTasks = new List<Task> { eggsTask, baconTask, toastTask };
while (breakfastTasks.Count > 0)
{
    Task finishedTask = await Task.WhenAny(breakfastTasks);
    if (finishedTask == eggsTask)
    {
        Console.WriteLine("eggs are ready");
    }
    else if (finishedTask == baconTask)
    {
        Console.WriteLine("bacon is ready");
    }
    else if (finishedTask == toastTask)
    {
        Console.WriteLine("toast is ready");
    }
    breakfastTasks.Remove(finishedTask);
}
```

进行所有这些更改之后，代码的最终版本将如下所示：<a id="final-version"></a>
:::code language="csharp" source="snippets/index/AsyncBreakfast-final/Program.cs" highlight="9-40":::

:::image type="content" source="media/whenany-async-breakfast.png" alt-text="when any 异步早餐":::

异步准备的早餐的最终版本大约花费了 15 分钟，因为一些任务并行运行，并且代码同时监视多个任务，只在需要时才执行操作。

此最终代码是异步的。 它更为准确地反映了一个人做早餐的流程。 将上述代码与本文中的第一个代码示例进行比较。 阅读代码时，核心操作仍然很明确。 你可以按照阅读本文开始时早餐制作说明的相同方式阅读此代码。 `async` 和 `await` 的语言功能支持每个人做出转变以遵循这些书面指示：尽可能启动任务，不要在等待任务完成时造成阻塞。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [探索异步程序的真实场景](../../../async.md)
