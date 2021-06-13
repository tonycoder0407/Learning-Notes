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

> 格式设置是指将类、结构或枚举值的实例转换为其字符串表示形式的过程，通常使得最终的字符串可以显示给用户，或者进行反序列化以还原为原始数据类型。 此转换可能面临一系列挑战：
>
> - 在内部存储值的方式不一定反映用户想要查看它们的方式。 例如，电话号码可以存储为 8009999999 格式，但此格式并非是用户友好的格式。 该电话号码应显示为 800-999-9999。 有关以这种方式设置数字格式的示例，请参见 [自定义格式字符串](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#custom-format-strings) 一节。
> - 有时对象到其字符串表示形式的转换不是直观的。 例如，不清楚 Temperature 对象或 Person 对象的字符串表示形式应如何显示。 有关以各种方式设置 Temperature 对象格式的示例，请参见 [标准格式字符串](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#standard-format-strings) 一节。
> - 值通常需要区分区域性的格式。 例如，在使用数字表示货币值的应用程序中，数字字符串应包括当前区域性的货币符号、组分隔符（在大多数区域性中，组分隔符为千位分隔符）和小数点符号。 有关示例，请参阅[使用格式提供程序进行区分区域性的格式设置](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#culture-sensitive-formatting-with-format-providers)部分。
> - 应用程序可能需要以不同方式显示相同的值。 例如，应用程序可能通过显示名称的字符串表示形式来表示一个枚举成员，或通过显示基础值来表示该枚举成员。 有关以不同方式设置 [DayOfWeek](https://docs.microsoft.com/zh-cn/dotnet/api/system.dayofweek) 枚举成员格式的示例，请参见 [标准格式字符串](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#standard-format-strings) 一节。

Serialization作为传输时重要的操作，实现了将对象转换成序列（例如二进制流）——而在展示时就十分需要反序列化进行正确的映射。

> ## .NET 中的格式设置
>
> 格式设置的基本机制是 [Object.ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring) 方法的默认实现。有关信息，请参阅本主题稍后将介绍的[使用 ToString 方法的默认格式设置](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#default-formatting-using-the-tostring-method)部分。 不过，.NET 提供了几种方法来修改和扩展其默认格式设置支持。 其中包括：
>
> - 重写 [Object.ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring) 方法以定义对象值的自定义字符串表示形式。 有关详细信息，请参见本主题后面的[重写 ToString 方法](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#override-the-tostring-method)部分。
>
> - 定义格式说明符，格式说明符允许对象值的字符串表示形式采用多种形式。 例如，以下语句中的“X”格式说明符将整数转换为十六进制值的字符串表示形式。
>
>   ```csharp
>   int integerValue = 60312;
>   Console.WriteLine(integerValue.ToString("X"));   // Displays EB98.
>   ```
>
>   有关格式说明符的更多信息，请参见 [ToString 方法和格式字符串](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#the-tostring-method-and-format-strings) 部分。
>
> - 使用格式提供程序以利用特定区域性的格式设置约定。 例如，以下语句通过使用 en-US 区域性的格式设置约定来显示货币值。
>
>   ```csharp
>   double cost = 1632.54;
>   Console.WriteLine(cost.ToString("C",
>                     new System.Globalization.CultureInfo("en-US")));
>   // The example displays the following output:
>   //       $1,632.54
>   ```
>
>   有关使用格式提供程序进行格式设置的详细信息，请参阅[格式提供程序](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#culture-sensitive-formatting-with-format-providers)部分。
>
> - 实现 [IFormattable](https://docs.microsoft.com/zh-cn/dotnet/api/system.iformattable) 接口可以支持使用 [Convert](https://docs.microsoft.com/zh-cn/dotnet/api/system.convert) 类的字符串转换以及复合格式设置。 有关更多信息，请参见 [IFormattable 接口](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#the-iformattable-interface) 部分。
>
> - 使用复合格式设置来嵌入较大字符串中值的字符串表示形式。 有关更多信息，请参见 [复合格式设置](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#composite-formatting) 部分。
>
> - 实现 [ICustomFormatter](https://docs.microsoft.com/zh-cn/dotnet/api/system.icustomformatter) 和 [IFormatProvider](https://docs.microsoft.com/zh-cn/dotnet/api/system.iformatprovider) 可以提供完全自定义的格式设置解决方案。 有关更多信息，请参见 [使用 ICustomFormatter 进行自定义格式设置](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/formatting-types#custom-formatting-with-icustomformatter) 部分。

不可避免的，既然第一项是通过重写`ToString()`方法实现的，少不得要剖析一下。

先贴示例代码：

```csharp
using System;

public class Automobile
{
   // No implementation. All members are inherited from Object.
}

public class Example
{
   public static void Main()
   {
      Automobile firstAuto = new Automobile();
      Console.WriteLine(firstAuto);
   }
}
// The example displays the following output:
//       Automobile
```

这里和Java很像，`System.out.println()`和`Console.WriteLine()`都可以接受Object类的对象并在这之后调用对象的`toString()`或者`ToString()`方法。

那么就来看看`ToString()`的文档吧。

### `public virtual string? ToString ();`

> 方法的默认实现 [ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) 返回的类型的完全限定名称 [Object](https://docs.microsoft.com/zh-cn/dotnet/api/system.object?view=net-5.0) ，如下面的示例所示。

```csharp
Object obj = new Object();
Console.WriteLine(obj.ToString());

// The example displays the following output:
//      System.Object
```

> ### Windows 运行时的说明
>
> 在 [ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) Windows 运行时中的类上调用方法时，它将为不重写的类提供默认行为 [ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) 。 这是 .NET Framework 为 Windows 运行时提供的支持的一部分 (参阅对 [Windows 应用商店应用的 .NET Framework 支持和 Windows 运行时](https://docs.microsoft.com/zh-cn/dotnet/standard/cross-platform/support-for-windows-store-apps-and-windows-runtime)) 。 Windows 运行时中的类不继承 [Object](https://docs.microsoft.com/zh-cn/dotnet/api/system.object?view=net-5.0) ，并且不总是实现 [ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) 。 但是， [ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) [Equals(Object)](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.equals?view=net-5.0#System_Object_Equals_System_Object_) [GetHashCode](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.gethashcode?view=net-5.0) 当你在 c # 或 Visual Basic 代码中使用它们时，它们始终具有、和方法，而 .NET Framework 提供这些方法的默认行为。
>
> 从 .NET Framework 4.5.1 开始，公共语言运行时对 Windows 运行时对象使用 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable.tostring#Windows_Foundation_IStringable_ToString) ，然后再回退到的默认实现 [Object.ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) 。

> #### Windows 运行时和 IStringable 接口
>
> 从 Windows 8.1 开始，Windows 运行时包括 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) 接口，该接口的单一方法 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable.tostring#Windows_Foundation_IStringable_ToString)提供的基本格式设置支持可与提供的类似 [Object.ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) 。 若要防止歧义，不应在托管类型上实现 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) 。
>
> 当本机代码或使用 JavaScript 或 c + +/CX 等语言编写的代码调用托管对象时，它们似乎实现了 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable)。 [Object.ToString](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0) 如果未在托管对象上实现[IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) ，则公共语言运行时自动将来自 IStringable 的调用路由到。
>
> **警告**
>
> 由于公共语言运行时自动实现 Windows 应用商店应用中所有托管类型的 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) ，因此，我们建议你不要提供自己的 `IStringable` 实现。 在 `IStringable` `ToString` 从 Windows 运行时、c + +/Cx 或 JavaScript 调用时，实现可能会导致意外的行为。
>
> 如果选择在 Windows 运行时组件中导出的公共托管类型中实现 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) ，则以下限制适用：
>
> - 你只能在 "类实现" 关系中定义 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) 接口，如下所示：
>
>   ```csharp
>   public class NewClass : IStringable
>   ```
>
> - 无法在接口上实现 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) 。
>
> - 不能将参数声明为 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable)类型。
>
> - [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) 不能是方法、属性或字段的返回类型。
>
> - 你无法通过使用如下所示的方法定义，从基类中隐藏 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) 实现：
>
>   ```csharp
>   public class NewClass : IStringable  
>   {  
>      public new string ToString()  
>      {  
>         return "New ToString in NewClass";  
>      }  
>   }  
>   ```
>
>   相反， [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable.tostring#Windows_Foundation_IStringable_ToString) 实现必须始终重写基类实现。 只能通过对强类型类实例调用 `ToString` 实现来隐藏该实现。
>
> 请注意，在各种条件下，从本机代码到实现 [IStringable](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable) 或隐藏其 [ToString](https://docs.microsoft.com/zh-cn/uwp/api/windows.foundation.istringable.tostring#Windows_Foundation_IStringable_ToString) 实现的托管类型的调用可能会产生意外行为。

读了几遍，感觉核心意思是，由于Windows运行时提供了IStringable这个接口，这个接口的ToString会对.NET提供的默认ToString做一个binding，所以默认实现有概率出现奇怪的bug——所以一定要老老实实的重写，而且不要轻易继承IStringable这个Interface——接口不具备实现这个Interface的能力，这个Interface不能成为参数的类型，也不能是返回。在这个情形下，可以且仅可以通过类实现的关系定义，并利用这个接口。

（所以说了这么多就是别用x 微软果然对自己提供的东西都没有啥信心吗2333）

> **继承者说明**
>
> 实现自己的类型时，应重写 [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 方法以返回对这些类型有意义的值。 需要更好地控制格式设置的派生类 [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 可以实现 [IFormattable](https://docs.microsoft.com/zh-cn/dotnet/api/system.iformattable?view=net-5.0) 接口。 它的 [ToString(String, IFormatProvider)](https://docs.microsoft.com/zh-cn/dotnet/api/system.iformattable.tostring?view=net-5.0#System_IFormattable_ToString_System_String_System_IFormatProvider_) 方法使你能够定义用于控制格式设置的格式字符串，并使用 [IFormatProvider](https://docs.microsoft.com/zh-cn/dotnet/api/system.iformatprovider?view=net-5.0) 可为区域性特定的格式设置提供的对象。
>
> 方法的重写 [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 应遵循以下准则： -返回的字符串应该是友好的，可供人们阅读。
>
> -返回的字符串应唯一标识对象实例的值。
>
> -返回的字符串应尽可能短，以便调试器能够显示它。
>
> \- [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 重写不应返回 [Empty](https://docs.microsoft.com/zh-cn/dotnet/api/system.string.empty?view=net-5.0) 或为空字符串。
>
> \- [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 重写不应引发异常。
>
> -如果实例的字符串表示形式区分区域性或可通过多种方式进行格式化，则实现 [IFormattable](https://docs.microsoft.com/zh-cn/dotnet/api/system.iformattable?view=net-5.0) 接口。
>
> -如果返回的字符串包含敏感信息，则应首先请求适当的权限。 如果请求成功，则可以返回敏感信息;否则，应返回排除敏感信息的字符串。
>
> \- [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 重写应没有可观察到的副作用，以避免调试中的复杂。 例如，对方法的调用 [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 不应更改实例字段的值。
>
> -如果你的类型 (或 `Parse` 或 `TryParse` 方法、构造函数或从字符串) 实例化类型实例的其他静态方法实现分析方法，则应确保该方法返回的字符串 [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) 可转换为对象实例。

继承者说明这个翻译也很有意思。瞄了一眼原文确实是*Notes to Inheritors*，不过Object不是所有类的基类嘛233

所以这里应该是，对于任何想要通过重写`ToString()`方法的类而言，都应当遵守这些规范。

### 重写`ToString()`方法实现格式设置

兜兜转转终于回来了。实际上阅读文档的一大乐趣应该就在互相跳转的过程中，可以了解到一些开发者在设计过程中的妥协与思考。应当是不存在最好的设计，但是总可以存在更好的设计——在某个维度上可以通过取舍实现得到更多，也总是伴随着牺牲。

> 显示类型的名称这一用法往往有限，它不允许类型使用者区分实例。 但是，你可以重写 `ToString` 方法，以提供更有用的对象值表示形式。 下面的示例定义 `Temperature` 对象并重写其 `ToString` 方法，以便以摄氏度显示温度。
>
> ```csharp
> using System;
> 
> public class Temperature
> {
>    private decimal temp;
> 
>    public Temperature(decimal temperature)
>    {
>       this.temp = temperature;
>    }
> 
>    public override string ToString()
>    {
>       return this.temp.ToString("N1") + "°C";
>    }
> }
> 
> public class Example
> {
>    public static void Main()
>    {
>       Temperature currentTemperature = new Temperature(23.6m);
>       Console.WriteLine("The current temperature is " +
>                         currentTemperature.ToString());
>    }
> }
> // The example displays the following output:
> //       The current temperature is 23.6°C.
> ```
>
> 在 .NET 中，每个基元值类型的 `ToString` 方法已重写为显示对象值（而不是对象名称）。 下表显示每种基元类型的重写。 请注意，大多数重写方法调用 `ToString` 方法的另一个重载并向其传递用于定义其类型的一般格式的“G”格式说明符和表示当前区域性的 [IFormatProvider](https://docs.microsoft.com/zh-cn/dotnet/api/system.iformatprovider) 对象。

