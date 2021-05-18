# Harmony OS Briefly Intro

> 这一篇还是用中文写吧== 再搞英文文档时间就顶不住了QAQ

首先介绍一下主要的几个重要概念。

## FA & PA

> Ability是应用所具备能力的抽象，也是应用程序的重要组成部分。一个应用可以具备多种能力（即可以包含多个Ability），HarmonyOS支持应用以Ability为单位进行部署。Ability可以分为FA（Feature Ability）和PA（Particle Ability）两种类型，每种类型为开发者提供了不同的模板，以便实现不同的业务功能。

这里其实是华为这帮憨批命名的时候不检点，结果Page和Particle重了，搞得缩写很阴间。

众所周知，麻瓜写的程序里面主要有两种Ability，正如Ability这个词所描述的那样，这是一个应用所具有的两种能力。而能力又分为两种，可以被看见的以及看不见的，所以分为了Page Ability和Feature Ability。

### PA

Page Ability是带UI界面的能力，而Feature Ability是不带有UI界面的能力，所以进一步将FA分为Service Ability和Data Ability。正如命名一般，一个是为了数据持久化和提取操作，另一个是为了后台的业务常驻和唤醒等。

> Page模板（以下简称“Page”）是FA唯一支持的模板，用于提供与用户交互的能力。一个Page可以由一个或多个AbilitySlice构成，AbilitySlice是指应用的单个页面及其控制逻辑的总和。

单个AbilitySlice其实就是单个的页面。由于在页面上进行数据更新和展示可能需要不同的逻辑，所以引入了AbilitySlice的概念。可以通过跳转到其他PA的具体AbilitySlice实现应用间能力的调用。（e.g. 美团外卖内点击拨打电话按钮可以跳转到拨号app的PA。）

#### PA生命周期

![PA生命周期](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20210330203818.05372477209167764226774078468340:50520330103355:2800:32B72D67068401533B140F94334643F7FEA70513BB14BE58043EF5032AF30E0B.png?needInitFileName=true?needInitFileName=true?needInitFileName=true)

这里Page的生命周期与一个进程的生命周期有类似的地方。首先通过`onStart()`进入就绪状态，随后通过`onActive()`进入前台，`onInactive()`进入后台，在完全不可见以后通过`onBackground()`进入后台。随后由于`onForeground()`该Ability被重新唤醒，或者由于内存不足等原因杀死该Ability后（此时也会调用`onStop()`）重新调用`onStart()`唤醒。一种特殊情况是由于主动退出会调用`onStop()`。

#### AbilitySlice生命周期

> AbilitySlice作为Page的组成单元，其生命周期是依托于其所属Page生命周期的。AbilitySlice和Page具有相同的生命周期状态和同名的回调，当Page生命周期发生变化时，它的AbilitySlice也会发生相同的生命周期变化。此外，AbilitySlice还具有独立于Page的生命周期变化，这发生在同一Page中的AbilitySlice之间导航时，此时Page的生命周期状态不会改变。

**重点：**

> AbilitySlice生命周期回调与Page的相应回调类似，因此不再赘述。由于AbilitySlice承载具体的页面，开发者必须重写AbilitySlice的onStart()回调，并在此方法中通过setUIContent()方法设置页面，如下所示：

```java
    @Override
    protected void onStart(Intent intent) {
        super.onStart(intent);

        setUIContent(ResourceTable.Layout_main_layout);
    }
```

使用Java处理导航时一定要通过setUIContent()来实现前端页面的初始化，否则会啥都看不见x

> AbilitySlice实例创建和管理通常由应用负责，系统仅在特定情况下会创建AbilitySlice实例。例如，通过导航启动某个AbilitySlice时，是由系统负责实例化；但是在同一个Page中不同的AbilitySlice间导航时则由应用负责实例化。

这里并不怎么复杂，主要意思是跨app调用的时候会出这个问题。

但是这里更强调了这个函数重新实现的重要性：如果写到别的地方，系统就会调用出来一些奇怪的东西x

####  Page与AbilitySlice生命周期关联

> 当AbilitySlice处于前台且具有焦点时，其生命周期状态随着所属Page的生命周期状态的变化而变化。当一个Page拥有多个AbilitySlice时，例如：MyAbility下有FooAbilitySlice和BarAbilitySlice，当前FooAbilitySlice处于前台并获得焦点，并即将导航到BarAbilitySlice，在此期间的生命周期状态变化顺序为：
>
> 1. FooAbilitySlice从ACTIVE状态变为INACTIVE状态。
> 2. BarAbilitySlice则从INITIAL状态首先变为INACTIVE状态，然后变为ACTIVE状态（假定此前BarAbilitySlice未曾启动）。
> 3. FooAbilitySlice从INACTIVE状态变为BACKGROUND状态。
>
> 对应两个slice的生命周期方法回调顺序为：
>
> FooAbilitySlice.onInactive() --> BarAbilitySlice.onStart() --> BarAbilitySlice.onActive() --> FooAbilitySlice.onBackground()
>
> 在整个流程中，MyAbility始终处于ACTIVE状态。但是，当Page被系统销毁时，其所有已实例化的AbilitySlice将联动销毁，而不仅是处于前台的AbilitySlice。

这里没啥说的，主要强调一下顺序：是先释放资源再申请新的资源。很简单且容易想到的一个点，但是处理逻辑的时候要注意函数调用的顺序，否则容易出不被检测到的bug（x

### AbilitySlice导航

#### 同一Page内导航

> 当发起导航的AbilitySlice和导航目标的AbilitySlice处于同一个Page时，您可以通过present()方法实现导航。如下代码片段展示通过点击按钮导航到其他AbilitySlice的方法：
>
> ```java
>     @Override
>     protected void onStart(Intent intent) {
> 
>         ...
>         Button button = ...;
>         button.setClickedListener(listener -> present(new TargetSlice(), new Intent()));
>         // 一个很简单的lambda表达式，给button的ClickedListener绑定了一个新的listener对象，该对象的present方法传入了目标AbilitySlice的一个新实例和一个新的Intent对象（后续有详细说明）。
>         ...
> 
>     }
> ```
>
> 如果开发者希望在用户从导航目标AbilitySlice返回时，能够获得其返回结果，则应当使用presentForResult()实现导航。用户从导航目标AbilitySlice返回时，系统将回调onResult()来接收和处理返回结果，开发者需要重写该方法。返回结果由导航目标AbilitySlice在其生命周期内通过setResult()进行设置。
>
> ```java
>     @Override
>     protected void onStart(Intent intent) {
> 
>         ...
>         Button button = ...;
>         button.setClickedListener(listener -> presentForResult(new TargetSlice(), new Intent(), 0));
>         // 注意一点，这里的0是requestCode，相当于状态码，用于区分正常运行返回与异常处理结果
>         // 后面的onResult函数调用了一个resultIntent，这个resultIntent就是这里new出来的无内容Intent。
>         ...
> 
>     }
> 
>     @Override
>     protected void onResult(int requestCode, Intent resultIntent) {
>         if (requestCode == 0) {
>             // Process resultIntent here.
>         }
>     }
> ```
>
> 系统为每个Page维护了一个AbilitySlice实例的栈，每个进入前台的AbilitySlice实例均会入栈。当开发者在调用present()或presentForResult()时指定的AbilitySlice实例已经在栈中存在时，则栈中位于此实例之上的AbilitySlice均会出栈并终止其生命周期。前面的示例代码中，导航时指定的AbilitySlice实例均是新建的，即便重复执行此代码（此时作为导航目标的这些实例是同一个类），也不会导致任何AbilitySlice出栈。

这里注意一下，透漏了一点实现方案。

AbilitySlice是归属于具体的Page的，而每一个Page均有一个栈来存放自己的AbilitySlice实例。当外界调用某个特定的AbilitySlice的时候就会检查

