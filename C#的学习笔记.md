# C#的学习笔记

www 着实是 又来突击了==

所以只好再开一个坑了QAQ

## I/O Parts

要学一门编程语言，学会做IO似乎是接近最基本的事情了emm

本来打算参考[菜鸟教程](https://www.runoob.com/csharp/csharp-tutorial.html)的内容来着

结果并没有单独的一章 只好上微软文档查了23333333 

那么整体应该分为两个部分

* 文件流的I/O
* 控制台输出的格式操作

### 文件流I/O

先贴链接 [网站链接](https://docs.microsoft.com/zh-cn/dotnet/standard/io/)

> 文件和流 I/O（输入/输出）是指在存储媒介中传入或传出数据。 在 .NET 中，`System.IO` 命名空间包含允许以异步方式和同步方式对数据流和文件进行读取和写入操作的类型。 这些命名空间还包含对文件执行压缩和解压缩的类型，以及通过管道和串行端口启用通信的类型。
>
> 文件是一个由字节组成的有序的命名集合，它具有永久存储。 在处理文件时，你将处理目录路径、磁盘存储、文件和目录名称。 相反，流是一个字节序列，可用于对后备存储进行读取和写入操作，后备存储可以是多个存储媒介之一（例如，磁盘或内存）。 正如存在除磁盘之外的多种后备存储一样，也存在除文件流之外的多种流（如网络、内存和管道流）。

对于流这个概念应该已经说过很多了。以Java的设计举例的话，主要分输入和输出流，再通过来源进行具体划分，总而言之是基于基类接口然后扩展实现的。

而文件作为存储的一个单元而言，本来应该通过FileStream直接处理好。但是这个吧，出于众所周知的兼容性考虑，.NET似乎还是专门为File设计了一套规范（其实就是不写不行 不然光路径问题都裂开了==）23333

以及还有一个专门的`Microsoft.VisualBasic.FileIO.FileSystem`类供VB使用==

于是文件的部分先咕咕了 等到后面要还具体文件操作再看23333

#### I/O

这个才是我特别想写的部分。简而言之，其实就是想知道如何使用输入时的`Console.ReadKey()`或者是输出时的`Console.WriteLine()`这种函数，每次对于大括号括起来的内容总是感觉很神奇23333

所以这个部分先主要针对`System.Console`类进行讨论

> .NET 应用程序可以使用 [System.Console](https://docs.microsoft.com/zh-cn/dotnet/api/system.console) 类在控制台中读取和写入字符。 读取自控制台的数据是从标准输入流读取的，而写入到控制台的数据将写入标准输出流，并且写入控制台的错误数据将写入标准错误输出流。 应用程序启动时，这些数据流会自动与控制台关联，并分别表示为 [In](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.in)、[Out](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.out) 和 [Error](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.error) 属性。
>
> [Console.In](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.in) 属性的值是 [System.IO.TextReader](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.textreader) 对象，而 [Console.Out](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.out) 和 [Console.Error](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.error) 属性的值都是 [System.IO.TextWriter](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.textwriter) 对象。 你可以将这些属性与不表示控制台的流关联，以便可以将该流指向不同的输入位置或输出位置。 例如，你可以通过将 [Console.Out](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.out) 属性设置为 [System.IO.StreamWriter](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.streamwriter) 将输出重定向到一个文件，这将通过 [Console.SetOut](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.setout) 方法封装 [System.IO.FileStream](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.filestream)。 [Console.In](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.in) 和 [Console.Out](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.out) 属性不需要引用相同流。
>
> 如果不存在控制台（比如在 Windows 窗体应用程序中），写入标准输出流的输出将不可见，因为没有可以将信息写入的控制台。 将信息写入不可访问的控制台不会引发异常。 （例如在 Visual Studio 的项目属性页中，你始终可以将应用程序类型更改为“控制台应用程序”）。
>
> 下面的示例演示如何从标准输入和输出流中读取数据以及向其中写入数据。 请注意，这些流可以使用和方法进行重定向 [SetIn](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.setin?view=net-5.0) [SetOut](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.setout?view=net-5.0) 。

```c#
using System;

public class Example {
    public static void Main()
    {
        Console.Write("Hello ");
        Console.WriteLine("World!");
        Console.Write("Enter your name: ");
        String name = Console.ReadLine();
        Console.Write("Good day, ");
        Console.Write(name);
        Console.WriteLine("!");
    }
}
// The example displays output similar to the following:
//       Hello World!
//       Enter your name: James
//       Good day, James!
```

这个`Write`和`WriteLine`就是很逊啦

##### ReadKey()

>  重载为`public static ConsoleKeyInfo ReadKey (bool intercept);`

那`ReadKey`好像很懂诶

就皮一下（（

大概内容是，ReadKey可以加一个bool参数，默认是false，设置为true以后不会再返回读取到了什么键。

返回：**ConsoleKeyInfo**

>  一个对象，描述 [ConsoleKey](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekey?view=net-5.0) 常数和对应于按下的控制台键的 Unicode 字符（如果存在这样的字符）。 [ConsoleKeyInfo](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekeyinfo?view=net-5.0) 对象还在 [ConsoleModifiers](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolemodifiers?view=net-5.0) 值的按位组合中描述是否在按下控制台键的同时按下了一个或多个 Shift、Alt 和 Ctrl 修改键。
>
> 继承
>
> [Object](https://docs.microsoft.com/zh-cn/dotnet/api/system.object?view=net-5.0)=>[ValueType](https://docs.microsoft.com/zh-cn/dotnet/api/system.valuetype?view=net-5.0)=>ConsoleKeyInfo
>
> 实现
>
> [IEquatable](https://docs.microsoft.com/zh-cn/dotnet/api/system.iequatable-1?view=net-5.0)\<[ConsoleKeyInfo](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekeyinfo?view=net-5.0)\>

示例代码

```c#
using System;

class Example
{
   public static void Main()
   {
      ConsoleKeyInfo cki;
      // Prevent example from ending if CTL+C is pressed.
      Console.TreatControlCAsInput = true;

      Console.WriteLine("Press any combination of CTL, ALT, and SHIFT, and a console key.");
      Console.WriteLine("Press the Escape (Esc) key to quit: \n");
      do
      {
         cki = Console.ReadKey();
         Console.Write(" --- You pressed ");
         if((cki.Modifiers & ConsoleModifiers.Alt) != 0) Console.Write("ALT+");
         if((cki.Modifiers & ConsoleModifiers.Shift) != 0) Console.Write("SHIFT+");
         if((cki.Modifiers & ConsoleModifiers.Control) != 0) Console.Write("CTL+");
         Console.WriteLine(cki.Key.ToString());
       } while (cki.Key != ConsoleKey.Escape);
    }
}
// This example displays output similar to the following:
//       Press any combination of CTL, ALT, and SHIFT, and a console key.
//       Press the Escape (Esc) key to quit:
//
//       a --- You pressed A
//       k --- You pressed ALT+K
//       ► --- You pressed CTL+P
//         --- You pressed RightArrow
//       R --- You pressed SHIFT+R
//                --- You pressed CTL+I
//       j --- You pressed ALT+J
//       O --- You pressed SHIFT+O
//       § --- You pressed CTL+U
```

属性

| 字段                                                         | 解释                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Key](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekeyinfo.key?view=net-5.0#System_ConsoleKeyInfo_Key) | 获取当前 [ConsoleKeyInfo](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekeyinfo?view=net-5.0) 对象表示的控制台键。 |
| [KeyChar](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekeyinfo.keychar?view=net-5.0#System_ConsoleKeyInfo_KeyChar) | 获取当前 [ConsoleKeyInfo](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekeyinfo?view=net-5.0) 对象表示的 Unicode 字符。 |
| [Modifiers](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekeyinfo.modifiers?view=net-5.0#System_ConsoleKeyInfo_Modifiers) | 获取 [ConsoleModifiers](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolemodifiers?view=net-5.0) 值的一个按位组合，指定与控制台键同时按下的一个或多个修改键。 |

##### ReadLine()

相比于ReadKey而言，个人认为ReadLine()应该会更频繁的被用来做内容输入。

当然依靠流的ReadInt32()这种函数个人感觉会更为的高效，对一个二进制流进行读4个字节这样的操作，好像还莫名感觉十分优雅233333

言归正传，还是先说正经的——这次从报错看起。

> *Exceptions*
>
> [IOException](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.ioexception?view=net-5.0)
>
> 出现 I/O 错误。
>
> [OutOfMemoryException](https://docs.microsoft.com/zh-cn/dotnet/api/system.outofmemoryexception?view=net-5.0)
>
> 没有足够的内存来为返回的字符串分配缓冲区。
>
> [ArgumentOutOfRangeException](https://docs.microsoft.com/zh-cn/dotnet/api/system.argumentoutofrangeexception?view=net-5.0)
>
> 下一行中的字符的字符数大于 [MaxValue](https://docs.microsoft.com/zh-cn/dotnet/api/system.int32.maxvalue?view=net-5.0)。

别问我为什么要写Exceptions

因为微软官网翻译的是例外hhhhhhhhh

这里很有意思的一点是，实际上ReadLine是通过将内容不断读到缓冲区，然后在遇到类似**\r\n**这样的标记以后停止读入并输出的。

 这里歪个楼

这个MaxValue点开以后是Int32.MaxValue，也就是2147483647。

所以更有意思的事情出现了——Int32的MaxValue放到String的长度上，汉字的char应该可以用3到4个byte表示——所以可以有好多个G大小的字符串嘛233333

很遗憾的是，不可以。

.NET框架有一个很重要的限制：单个对象大小不得超过2GB。

这样的话，按照一个Unicode编码的字符每个占2 bytes，也就是String的最大长度一定小于等于1,073,741,823。

那这么看的话，理论上32位系统可以靠两个字符串把内存炸了（bushi）

--------------

然而实际的情况是，8G内存的Surface Pro 7用尽心思，终于发现：

确实是没跑出来最大值2333333333

经过验证不会报`System.OutOfMemoryException`的最大长度定格在了1,073,741,790。

（这个字符串是直接用`string s = new string(' ',1073741790);`造出来的）

也就是2,147,483,580 bytes

还剩68 bytes

我一开始猜测可能是因为8G内存跑个VS就很吃力了，再加上个2G内存的字符串会有压力，直接崩溃那种

然后突然想到，正好还有个length属性一直没管，这个Int32加上，再加上C#结尾或许\0\0的结尾就差不多了

（这么解释好像挺完美的）

仔细一想不太对，Int32才4个byte，或许是有好多个指针占了这68个字节的空间吧

具体落到硬件细节的话，我的Surface是双通道的，可能需要两根内存一起凑一个2G出来，这样在一点点逐个增加的过程中就有可能出现需要多个指针的问题。

所以实际和理论总是容易有那么点小小的偏差，具体这个问题怎么样已经折腾一天了23333 所以就先到此为止吧

*******************

##### Read()

本来不怎么想写这个的

但是看到这个很熟悉的函数名字就想起了老板之前讲Java的时候说

> `Read()`这个函数很重要，因为实现了Read就可以实现对流的任何读取操作——会读一个字节就会以任意方式读任意字节。

下面是官网的一点翻译（差不多烂）

> 当 [Read](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.read?view=net-5.0) 你键入输入字符时，方法会阻止其返回; 当你按下键时，该方法将终止 [Enter](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekey?view=net-5.0#System_ConsoleKey_Enter) 。 按 Enter 会将与平台相关的行终止序列附加到输入 (例如，Windows 会将回车换行符序列附加) 。 对方法的后续调用 [Read](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.read?view=net-5.0) 每次检索一次输入一个字符。 检索最后一个字符后，将 [Read](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.read?view=net-5.0) 再次阻止其返回，并重复周期。
>
>  **重要**
>
> [ReadLine](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.readline?view=net-5.0)方法，或者 [KeyAvailable](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.keyavailable?view=net-5.0) 属性和 [ReadKey](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.readkey?view=net-5.0) 方法比使用方法更可取 [Read](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.read?view=net-5.0) 。
>
> 请注意，除非您执行下列操作之一，否则方法不会返回-1：
>
> - 同时按下 [Control](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolemodifiers?view=net-5.0#System_ConsoleModifiers_Control) 修改键和 [Z](https://docs.microsoft.com/zh-cn/dotnet/api/system.consolekey?view=net-5.0#System_ConsoleKey_Z) 控制台键 (Ctrl + Z) ，这将指示文件尾条件。
> - 按指示文件尾条件的等效键，例如 Windows 中的 F6 函数键。
> - 将输入流重定向到具有实际文件尾字符的源（如文本文件）。

##### SetIn(), SetOut(), SetError()

将 [In](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.in?view=net-5.0#System_Console_In) 属性设置为指定的 [TextReader](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.textreader?view=net-5.0) 对象。

将 [Out](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.out?view=net-5.0#System_Console_Out) 属性设置为面向 [TextWriter](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.textwriter?view=net-5.0) 对象。

将 [Error](https://docs.microsoft.com/zh-cn/dotnet/api/system.console.error?view=net-5.0#System_Console_Error) 属性设置为指定的 [TextWriter](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.textwriter?view=net-5.0) 对象。

功能很简单，这里写下Exception：

> [ArgumentNullException](https://docs.microsoft.com/zh-cn/dotnet/api/system.argumentnullexception?view=net-5.0)
>
> `newOut` 为 `null`。
>
> [SecurityException](https://docs.microsoft.com/zh-cn/dotnet/api/system.security.securityexception?view=net-5.0)
>
> 调用方没有所要求的权限。

就很明确了2333 没啥可说的

***************

## .NET 中的格式类型

这个理论上应该是给`Write()`, `WriteLine()`函数的注解

但是我还是想用一个更大的标题——因为这个还会少量的牵涉到和具体对象属性获取的一点内容

所以就用了二级标题，和I/O Parts这个顶级标题并列

这个题目从某种角度讲也是我想要开这个坑的First and foremost reason了



