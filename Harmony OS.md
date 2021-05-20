# Harmony OS Develop Briefly Intro

> 这一篇还是用中文写吧== 再搞英文文档时间就顶不住了QAQ
>
> 以及这篇的定位其实算个人的一点阅读笔记，带点吐槽那种。
>
> 主要用途是自学，当然宁如果看到了指点一下小菜鸟也是极好的www
>
> 求轻点喷（x

首先介绍一下主要的几个重要概念。

大致按照文档的方式进行总结，但是会根据个人的喜好进行微调，一些觉得不适合在当前文档展开的会适当后移。

希望看完以后会大概有一些想法吧。

重要的概念个人认为有这几个部分：

* Ability（FA & PA）

* 线程

* UI & Media

* 网络连接

* 设备管理

剩下的会按照进度往后稍稍，毕竟考试周这么写文档跟二货一样（x

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

AbilitySlice是归属于具体的Page的，而每一个Page均有一个栈来存放自己的AbilitySlice实例。当外界调用某个特定的AbilitySlice的时候就会检查该AbilitySlice在栈中的位置，如果在栈上还有别的AbilitySlice实例会将其弹出。相当于某个软件的部分子界面（后入栈的一般都会是子界面？）有可能会被销毁。

### 不同应用间导航以及跨设备流转在后文说明。

## FA

FA主要分为Data Ability和Service Ability。

### Service Ability（本部分简称为SA）

#### 综述

> 基于Service模板的Ability（以下简称“Service”）主要用于后台运行任务（如执行音乐播放、文件下载等），但不提供用户交互界面。Service可由其他应用或Ability启动，即使用户切换到其他应用，Service仍将在后台继续运行。
>
> Service是单实例的。在一个设备上，相同的Service只会存在一个实例。如果多个Ability共用这个实例，只有当与Service绑定的所有Ability都退出后，Service才能够退出。由于Service是在主线程里执行的，因此，如果在Service里面的操作时间过长，开发者必须在Service里创建新的线程来处理（详见[线程间通信](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/inter-thread-overview-0000000000038958)），防止造成主线程阻塞，应用程序无响应。

这里主要说明一下SA的用途：后台服务的单例设计模式。SA作为后台进程将持续运行至任务结束或者被主动打断。SA可以被应用或者Ability启用，但是由于单例的SA可以被不同的Ability绑定，所以SA的生命周期会长于任何一个与它绑定的Ability。此外，SA的单例设计也主动提升了其拥有的权限，在主线程内执行，所以需要保证该线程的操作不能**持续太久**，需要及时退出并创建新线程或者抛出异常。

#### 创建Service

> - onConnect()
>
>   在Ability和Service连接时调用，该方法返回IRemoteObject对象，用户可以在该回调函数中生成对应Service的IPC通信通道，以便Ability与Service交互。Ability可以多次连接同一个Service，系统会缓存该Service的IPC通信对象，只有第一个客户端连接Service时，系统才会调用Service的onConnect方法来生成IRemoteObject对象，而后系统会将同一个RemoteObject对象传递至其他连接同一个Service的所有客户端，而无需再次调用onConnect方法。
>
> - onDisconnect()
>
>   在Ability与绑定的Service断开连接时调用。

这里主要关注这两个函数。绑定Ability和Service时调用该方法可以返回一个**可以被复用的`IRemoteObject`对象**。这样设计时，`onConnect`函数在生命周期内应当只调用一次。所以在某个特定Ability多次调用同一个Service时要注意处理`IRemoteObject`对象时应当操作对象本身而非在`onConnect`函数中实现其他逻辑。

```java
public class ServiceAbility extends Ability {
    @Override
    public void onStart(Intent intent) {
        super.onStart(intent);
    }

    @Override
    public void onCommand(Intent intent, boolean restart, int startId) {
        super.onCommand(intent, restart, startId);
    }

    @Override
    public IRemoteObject onConnect(Intent intent) {
        // 函数仅在第一次连接时调用。当作构造函数来设计逻辑。
        return super.onConnect(intent);
    }

    @Override
    public void onDisconnect(Intent intent) {
        super.onDisconnect(intent);
    }

    @Override
    public void onStop() {
        super.onStop();
    }
}
```

#### 启动Service

> ​		如果Service需要与Page Ability或其他应用的Service Ability进行交互，则应创建用于连接的Connection。Service支持其他Ability通过connectAbility()方法与其进行连接。
>
> ​		在使用connectAbility()处理回调时，需要传入目标Service的Intent与IAbilityConnection的实例。IAbilityConnection提供了两个方法供开发者实现：onAbilityConnectDone()用来处理连接的回调，onAbilityDisconnectDone()用来处理断开连接的回调。
>
> - 启动本地设备Service的代码示例如下：
>
>   ```java
>   Intent intent = new Intent();
>   Operation operation = new Intent.OperationBuilder()        
>       .withDeviceId("")        
>       .withBundleName("com.domainname.hiworld.himusic")     
>       .withAbilityName("com.domainname.hiworld.himusic.ServiceAbility")       
>       .build();
>   intent.setOperation(operation);startAbility(intent);
>   ```
>
>   启动远程设备Service的代码示例如下：
>
>   ```java
>   Intent intent = new Intent();
>   Operation operation = new Intent.OperationBuilder()        
>       .withDeviceId("deviceId")        
>       .withBundleName("com.domainname.hiworld.himusic")        
>       .withAbilityName("com.domainname.hiworld.himusic.ServiceAbility")        
>       .withFlags(Intent.FLAG_ABILITYSLICE_MULTI_DEVICE) 
>       // 设置支持分布式调度系统多设备启动的标识        
>       .build();
>   intent.setOperation(operation);
>   startAbility(intent);
>   ```
>
>   执行上述代码后，Ability将通过startAbility() 方法来启动Service。
>
>   - 如果Service尚未运行，则系统会先调用onStart()来初始化Service，再回调Service的onCommand()方法来启动Service。
>   - 如果Service正在运行，则系统会直接回调Service的onCommand()方法来启动Service。
>
> - 停止Service
>
>   Service一旦创建就会一直保持在后台运行，除非必须回收内存资源，否则系统不会停止或销毁Service。开发者可以在Service中通过terminateAbility()停止本Service或在其他Ability调用stopAbility()来停止Service。
>
>   停止Service同样支持停止本地设备Service和停止远程设备Service，使用方法与启动Service一样。一旦调用停止Service的方法，系统便会尽快销毁Service。

老三样啦。先new一个Intent，然后定义启动的Operation，接着把Intent和Operation绑在一起就可以startAbility了。这里注意的一点是，统计信息放到onCommand比较好，由于onStart仅负责初始化，二次唤起不会调用onStart。停止Service也同样必要，在完成类似于文件下载、网络通信等行为后及时退出Service有利于资源回收。

####  Service Ability生命周期

提供了两种路径的生命周期运作方式。总体而言，生产和消费Ability是两种相反的操作。



![点击放大](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20210330203818.06600073438459576747498270270266:50520330103356:2800:ED431E4CA796C483A7A9697A0BA8B41EA868A23529BCEFB6316282C819D503D9.jpg?needInitFileName=true?needInitFileName=true?needInitFileName=true)



#### 前台Service

> 一般情况下，Service都是在后台运行的，后台Service的优先级都是比较低的，当资源不足时，系统有可能回收正在运行的后台Service。
>
> 在一些场景下（如播放音乐），用户希望应用能够一直保持运行，此时就需要使用前台Service。前台Service会始终保持正在运行的图标在系统状态栏显示。
>
> 使用前台Service并不复杂，开发者只需在Service创建的方法里，调用keepBackgroundRunning()将Service与通知绑定。调用keepBackgroundRunning()方法前需要在配置文件中声明ohos.permission.KEEP_BACKGROUND_RUNNING权限，同时还需要在配置文件中添加对应的backgroundModes参数。在onStop()方法中调用cancelBackgroundRunning()方法可停止前台Service。
>
> 使用前台Service的onStart()代码示例如下：
>
> ```java
> // 创建通知，其中1005为notificationId
> NotificationRequest request = new NotificationRequest(1005);
> NotificationRequest.NotificationNormalContent content = new NotificationRequest.NotificationNormalContent();
> content.setTitle("title").setText("text");
> // ps：为啥这里不能直接整个构造函数唔，感觉套的层数有点多了hh
> NotificationRequest.NotificationContent notificationContent = new NotificationRequest.NotificationContent(content);
> request.setContent(notificationContent);
> // 绑定通知，1005为创建通知时传入的notificationId
> keepBackgroundRunning(1005, request);
> ```
>
> 在配置文件中配置如下：
>
> ```json
> {        
>     "name": ".ServiceAbility",    
>     "type": "service",    
>     "visible": true,    
>     "backgroundModes": ["dataTransfer", "location"]
> }
> ```

前台Service最重要的一个特点是需要绑定到具体的前台通知上。这一点相当于将FA升级成为了PA。只有具体的焦点才能保证该Service不会被清理。

### Data Ability

> 使用Data模板的Ability（以下简称“Data”）有助于应用管理其自身和其他应用存储数据的访问，并提供与其他应用共享数据的方法。Data既可用于同设备不同应用的数据共享，也支持跨设备不同应用的数据共享。
>
> 数据的存放形式多样，可以是数据库，也可以是磁盘上的文件。Data对外提供对数据的增、删、改、查，以及打开文件等接口，这些接口的具体实现由开发者提供。

DataAbility相当于提供了一个与传统前后端开发中后台的一个重要功能：数据管理。

主要应用应当是与本地数据库和文件仓库进行数据的持久化以及查询提取利用。

> Data的提供方和使用方都通过URI（Uniform Resource Identifier）来标识一个具体的数据，例如数据库中的某个表或磁盘上的某个文件。HarmonyOS的URI仍基于URI通用标准，格式如下：
>
> ![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20210330203818.77215288438676699596441357847567:50520330103356:2800:202B79BCF8BE79666DB4FBE06EACAD15D65CEDB1B59A732EE67F18893557DED0.png?needInitFileName=true?needInitFileName=true?needInitFileName=true)
>
> - scheme：协议方案名，固定为“dataability”，代表Data Ability所使用的协议类型。
> - authority：设备ID。如果为跨设备场景，则为目标设备的ID；如果为本地设备场景，则不需要填写。
> - path：资源的路径信息，代表特定资源的位置信息。
> - query：查询参数。
> - fragment：可以用于指示要访问的子资源。
>
> URI示例：
>
> - 跨设备场景：dataability://*device_id*/*com.domainname.dataability.persondata*/*person*/*10*
> - 本地设备：dataability:///*com.domainname.dataability.persondata*/*person*/*10*

这里采用的URI设计应当与Restful API的设计思路相类似。

#### 创建Data

> 确定数据的存储方式，Data支持以下两种数据形式：
>
> - 文件数据：如文本、图片、音乐等。
> - 结构化数据：如数据库等。

类似的后台的结构化数据以及传输信息使用的json格式应当适用于数据库方式，而文件数据更多应当用于静态资源加载以及网络通信时的动态操作。

> Data提供了文件存储和数据库存储两组接口供用户使用。
>
> **文件存储**
>
> 开发者需要在Data中重写FileDescriptor openFile(Uri uri, String mode)方法来操作文件：uri为客户端传入的请求目标路径；mode为开发者对文件的操作选项，可选方式包含“r”(读), “w”(写), “rw”(读写)等。
>
> ohos.rpc.MessageParcel类提供了一个静态方法，用于获取MessageParcel实例。开发者可通过获取到的MessageParcel实例，使用dupFileDescriptor()函数复制待操作文件流的文件描述符，并将其返回，供远端应用访问文件。
>
> 示例：根据传入的uri打开对应的文件
>
> ```java
> private static final HiLogLabel LABEL_LOG = new HiLogLabel(HiLog.LOG_APP, 0xD00201, "Data_Log");
> // 这个类似常量的HiLogLabel用于区分log的来源，后文留坑补api解释（
> @Override
> public FileDescriptor openFile(Uri uri, String mode) throws FileNotFoundException {
>     // 创建messageParcel    
>     MessageParcel messageParcel = MessageParcel.obtain();
>     // obtain是静态方法，获取实例。
>     File file = new File(uri.getDecodedPathList().get(0));
>     //get(0)是获取URI完整字段中查询参数字段。    
>     if (mode == null || !"rw".equals(mode)) {        
>         file.setReadOnly();    
>         // 这里保证了：不是留空或者指明rw则设置为只读。
>         // r与w均无法对文件进行操作。
>     }    
>     FileInputStream fileIs = new FileInputStream(file);  
>     // 这个是java的api，得到文件后写进文件流
>     FileDescriptor fd = null;    
>     // 试图得到FileDescriptor
>     try {        
>         fd = fileIs.getFD();    
>     } catch (IOException e) {        
>         HiLog.info(LABEL_LOG, "failed to getFD");    
>     }
>     // 绑定文件描述符
>     // 这里返回了文件描述符的一份拷贝。
>     return messageParcel.dupFileDescriptor(fd);
> }
> ```

简而言之，文件存储仍然使用了Java的api进行包装。看上去保存文件直接使用java的方式保存到固定路径即可？但是问题在于文件的目录应该怎么写。留坑。

> **数据库存储**
>
> 1. 初始化数据库连接。
>
> 系统会在应用启动时调用onStart()方法创建Data实例。在此方法中，开发者应该创建数据库连接，并获取连接对象，以便后续和数据库进行操作。为了避免影响应用启动速度，开发者应当尽可能将非必要的耗时任务推迟到使用时执行，而不是在此方法中执行所有初始化。

由于onStart函数生命周期内必定只调用一次，所以很适合做数据库的连接。（？或许是这样吧x）

> 示例：初始化的时候连接数据库
>
> ```java
>private static final String DATABASE_NAME = "UserDataAbility.db";
> private static final String DATABASE_NAME_ALIAS = "UserDataAbility";
>private static final HiLogLabel LABEL_LOG = new HiLogLabel(HiLog.LOG_APP, 0xD00201, "Data_Log");
> private OrmContext ormContext = null;
>@Override
> public void onStart(Intent intent) {    
>  super.onStart(intent);    
>  DatabaseHelper manager = new DatabaseHelper(this); 
>     // 这个this是Ability
>  ormContext = manager.getOrmContext(DATABASE_NAME_ALIAS, DATABASE_NAME, BookStore.class);
>     // 看上去这个应该是需要单独配置来着？再挖个坑。
> }
>    ```
>    

主要的问题是这个数据库名称以及具体配置应当在哪里实现（x


> 2. 编写数据库操作方法。
>
> | 方法                                                         | 描述                   |
> | ------------------------------------------------------------ | ---------------------- |
> | `ResultSet query(Uri uri, String[] columns, DataAbilityPredicates predicates)` | 查询数据库             |
> | `int insert(Uri uri, ValuesBucket value)`                    | 向数据库中插入单条数据 |
> | `int batchInsert(Uri uri, ValuesBucket[] values)`            | 向数据库中插入多条数据 |
> | `int delete(Uri uri, DataAbilityPredicates predicates)`      | 删除一条或多条数据     |
> | `int update(Uri uri, ValuesBucket value, DataAbilityPredicates predicates)` | 更新数据库             |
> | `DataAbilityResult[] executeBatch(ArrayList<DataAbilityOperation> operations)` | 批量操作数据库         |
>
> 这些方法的使用说明如下：
>
>    - query()
>
> 该方法接收三个参数，分别是查询的目标路径，查询的列名，以及查询条件，查询条件由类DataAbilityPredicates构建。根据传入的列名和查询条件查询用户表的代码示例如下：
>
> ```java
> public ResultSet query(Uri uri, String[] columns, DataAbilityPredicates predicates) {
>     // uri是查询路径，columns是具体列名，predicates是条件
>     if (ormContext == null) {
>         HiLog.error(LABEL_LOG, "failed to query, ormContext is null");
>         return null;
>     }
> 
>     // 查询数据库
>     OrmPredicates ormPredicates = DataAbilityUtils.createOrmPredicates(predicates,User.class);
>     // DataAbilityPredicates是建立查询条件的类，传入后与具体类形成sql
>     ResultSet resultSet = ormContext.query(ormPredicates, columns);
>     // 奇怪的一点是这个uri没用到 23333
>     if (resultSet == null) {
>         HiLog.info(LABEL_LOG, "resultSet is null");
>     }
> 
>     // 返回结果
>     return resultSet;
> }
> ```
>
>    - insert()
>
> 该方法接收两个参数，分别是插入的目标路径和插入的数据值。其中，插入的数据由ValuesBucket封装，服务端可以从该参数中解析出对应的属性，然后插入到数据库中。此方法返回一个int类型的值用于标识结果。接收到传过来的用户信息并把它保存到数据库中的代码示例如下：
>
> ```java
> public int insert(Uri uri, ValuesBucket value) {    
>     // 参数校验    
>     if (ormContext == null) {        
>         HiLog.error(LABEL_LOG, "failed to insert, ormContext is null");        
>         return -1;    
>     }
>    // 构造插入数据    
>     User user = new User();    
>     user.setUserId(value.getInteger("userId"));   
>     user.setFirstName(value.getString("firstName"));
>     user.setLastName(value.getString("lastName"));   
>     user.setAge(value.getInteger("age"));   
>     user.setBalance(value.getDouble("balance"));
>     // 这里感觉ValuesBucket是一个json或者字典？2333
>    // 插入数据库    
>     boolean isSuccessful = ormContext.insert(user); 
>     // 注意insert函数的返回值是bool
>     if (!isSuccessful) {        
>         HiLog.error(LABEL_LOG, "failed to insert");        
>         return -1;    
>     }    
>     isSuccessful = ormContext.flush();   
>     if (!isSuccessful) {      
>         HiLog.error(LABEL_LOG, "failed to insert flush");   
>         return -1;    
>     }    
>     // insert操作分为两步，说明不经过flush的话或许会保留状态？
>     // 另一个也可以防止insert出错而bool返回是true（这里应该是插入成功但是状态并不完全正确。）
>     DataAbilityHelper.creator(this, uri).notifyChange(uri);   
>     // 这里或许是通过java的notifyAll对所有监听者作了状态变更说明？也有可能是自己实现的事件监听策略？（看到后面如果有的话就补个坑x
>     int id = Math.toIntExact(user.getRowId());   
>     // 这个函数没见过，补一个api手册的内容
>     return id;
> }
> ```
>
>    - batchInsert()
>
> 该方法为批量插入方法，接收一个ValuesBucket数组用于单次插入一组对象。它的作用是提高插入多条重复数据的效率。该方法系统已实现，开发者可以直接调用。
>
>    - delete()
>
> 该方法用来执行删除操作。删除条件由类DataAbilityPredicates构建，服务端在接收到该参数之后可以从中解析出要删除的数据，然后到数据库中执行。根据传入的条件删除用户表数据的代码示例如下：
>
> ```java
> public int delete(Uri uri, DataAbilityPredicates predicates) {    
>     if (ormContext == null) {        
>         HiLog.error(LABEL_LOG, "failed to delete, ormContext is null");        
>         return -1;    
>     }
>    OrmPredicates ormPredicates = DataAbilityUtils.createOrmPredicates(predicates,User.class);
>     int value = ormContext.delete(ormPredicates);    
>     DataAbilityHelper.creator(this, uri).notifyChange(uri);    
>     return value;
> }
> ```
>
>    - update()
>
> 此方法用来执行更新操作。用户可以在ValuesBucket参数中指定要更新的数据，在DataAbilityPredicates中构建更新的条件等。更新用户表的数据的代码示例如下：
>
> ```java
> public int update(Uri uri, ValuesBucket value, DataAbilityPredicates predicates) {
>     if (ormContext == null) {
>        HiLog.error(LABEL_LOG, "failed to update, ormContext is null");
>        return -1;
>    }
> 
>    OrmPredicates ormPredicates = DataAbilityUtils.createOrmPredicates(predicates,User.class);
>    int index = ormContext.update(ormPredicates, value);
>    HiLog.info(LABEL_LOG, "UserDataAbility update value:" + index);
>    DataAbilityHelper.creator(this, uri).notifyChange(uri);
>    return index;
> }
> ```
>
>    - executeBatch()
>
> 此方法用来批量执行操作。DataAbilityOperation中提供了设置操作类型、数据和操作条件的方法，用户可自行设置自己要执行的数据库操作。该方法系统已实现，开发者可以直接调用。

总而言之，DataAbility基本上是基于Java的实现，在创建数据方面需要重视`ValuesBucket`类，该类是进行数据封装的接口，而大部分操作都需要`DataAbilityPredicates`类的对象实现。

#### 注册UserDataAbility

> 配置文件中该字段在创建Data Ability时会自动创建，name与创建的Data Ability一致。
>
> 需要关注以下属性：
>
> - type: 类型设置为data
> - uri: 对外提供的访问路径，全局唯一
> - permissions: 访问该data ability时需要申请的访问权限

注意uri的命名问题。以及permissions是**要访问的数据**而非一般的存储权限。

### 访问Data

核心类：`DataAbilityHelper`，作为DataAbility的客户端访问Data。

#### 声明使用权限

> 如果待访问的Data声明了访问需要权限，则访问此Data需要在配置文件中声明需要此权限。声明请参考[权限申请字段说明](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/security-permissions-guidelines-0000000000029886#ZH-CN_TOPIC_0000001072906209__table73291742539)。
>
> ```json
> "reqPermissions": [    
>     {        
>         "name": "com.example.myapplication5.DataAbility.DATA"    
>     },    
>     // 访问文件还需要添加访问存储读写权限    
>     {        
>         "name": "ohos.permission.READ_USER_STORAGE"    
>     },    
>     {        
>         "name": "ohos.permission.WRITE_USER_STORAGE"    
>     }
> ]
> ```

这个权限的目的是访问者申请的，申请到才能够访问。

而数据提供方可以限制申请访问者必须得到xx权限才能申请。

因此这个数据**并不能**通过权限声明**完全保护**。

#### 创建DataAbilityHelper

> DataAbilityHelper为开发者提供了creator()方法来创建DataAbilityHelper实例。该方法为静态方法，有多个重载。最常见的方法是通过传入一个context对象来创建DataAbilityHelper对象。
>
> 获取helper对象示例：
>
> ```java
> DataAbilityHelper helper = DataAbilityHelper.creator(this);
> ```

这个写法让我感觉十分奇怪唔，挖个坑研究下这个context是什么牛马。

#### 访问Data Ability

> DataAbilityHelper为开发者提供了一系列的接口来访问不同类型的数据（文件、数据库等）。
>
> - 访问文件
>
>   DataAbilityHelper为开发者提供了FileDescriptor openFile(Uri uri, String mode)方法来操作文件。此方法需要传入两个参数，其中uri用来确定目标资源路径，mode用来指定打开文件的方式，可选方式包含“r”(读), “w”(写), “rw”(读写)，“wt”(覆盖写)，“wa”(追加写)，“rwt”(覆盖写且可读)。
>
>   该方法返回一个目标文件的FD（文件描述符），把文件描述符封装成流，开发者就可以对文件流进行自定义处理。
>
>   访问文件示例：
>
>   ```java
>   // 读取文件描述符
>   FileDescriptor fd = helper.openFile(uri, "r");
>   FileInputStream fis = new FileInputStream(fd);
>   // 使用文件描述符封装成的文件流，进行文件操作
>   ```

这里注意一下，openFIle会返回FileDescriptor是已经被override过的（（

> - 访问数据库
>
>   DataAbilityHelper为开发者提供了增、删、改、查以及批量处理等方法来操作数据库。
>
>   说明
>
>   对数据库的操作方法，详见[数据管理](https://developer.harmonyos.com/cn/docs/documentation/doc-guides/database-relational-overview-0000000000030046)中各数据库类型的开发指南。
>
>   
>
>   | 方法                                                         | 描述                   |
>   | ------------------------------------------------------------ | ---------------------- |
>   | `ResultSet query(Uri uri, String[] columns, DataAbilityPredicates predicates) ` | 查询数据库             |
>   | `int insert(Uri uri, ValuesBucket value) `                   | 向数据库中插入单条数据 |
>   | `int batchInsert(Uri uri, ValuesBucket[] values) `           | 向数据库中插入多条数据 |
>   | `int delete(Uri uri, DataAbilityPredicates predicates) `     | 删除一条或多条数据     |
>   | `int update(Uri uri, ValuesBucket value, DataAbilityPredicates predicates) ` | 更新数据库             |
>   | `DataAbilityResult[] executeBatch(ArrayList<DataAbilityOperation> operations) ` | 批量操作数据库         |
>
>   这些方法的使用说明如下：
>
>   - query()
>
>     查询方法，其中uri为目标资源路径，columns为想要查询的字段。开发者的查询条件可以通过DataAbilityPredicates来构建。查询用户表中id在101-103之间的用户，并把结果打印出来，代码示例如下：
>
>     ```java
>     DataAbilityHelper helper = DataAbilityHelper.creator(this);
>     // 构造查询条件
>     DataAbilityPredicates predicates = new DataAbilityPredicates();
>     predicates.between("userId", 101, 103);
>     // 进行查询
>     ResultSet resultSet = helper.query(uri, columns, predicates);
>     // 处理结果
>     resultSet.goToFirstRow();do {    
>         // 在此处理ResultSet中的记录
>         ;} while(resultSet.goToNextRow());
>     ```
>
>   - insert()
>
>     新增方法，其中uri为目标资源路径，ValuesBucket为要新增的对象。插入一条用户信息的代码示例如下：
>
>     ```java
>     DataAbilityHelper helper = DataAbilityHelper.creator(this);
>     // 构造插入数据
>     ValuesBucket valuesBucket = new ValuesBucket();
>     valuesBucket.putString("name", "Tom");
>     valuesBucket.putInteger("age", 12);
>     helper.insert(uri, valuesBucket);
>     ```
>
>   // 看到这里才发现put也是写好的。。好奇是怎么实现的emm
>
>   - batchInsert(）
>
>     批量插入方法，和insert()类似。批量插入用户信息的代码示例如下：
>
>     ```java
>     DataAbilityHelper helper = DataAbilityHelper.creator(this);
>     // 构造插入数据
>     ValuesBucket[] values = new ValuesBucket[2];
>     values[0] = new ValuesBucket();
>     values[0].putString("name", "Tom");
>     values[0].putInteger("age", 12);
>     values[1] = new ValuesBucket();
>     values[1].putString("name", "Tom1");
>     values[1].putInteger("age", 16);
>     helper.batchInsert(uri, values);
>     ```
>
>     // 实话讲没看出来批量了啥emm
>
>   - delete()
>
>     删除方法，其中删除条件可以通过DataAbilityPredicates来构建。删除用户表中id在101-103之间的用户，代码示例如下：
>
>     ```java
>     DataAbilityHelper helper = DataAbilityHelper.creator(this);
>     // 构造删除条件
>     DataAbilityPredicates predicates = new DataAbilityPredicates();
>     predicates.between("userId", 101, 103);
>     helper.delete(uri, predicates);
>     ```
>
>     画个重点，between是一个函数，回头整理下predicates支持的函数有啥吧
>
>   - update()
>
>     更新方法，更新数据由ValuesBucket传入，更新条件由DataAbilityPredicates来构建。更新id为102的用户，代码示例如下：
>
>     ```java
>     DataAbilityHelper helper = DataAbilityHelper.creator(this);
>     // 构造更新条件
>     DataAbilityPredicates predicates = new DataAbilityPredicates();
>     predicates.equalTo("userId", 102);
>     // 构造更新数据
>     ValuesBucket valuesBucket = new ValuesBucket();
>     valuesBucket.putString("name", "Tom");
>     valuesBucket.putInteger("age", 12);
>     helper.update(uri, valuesBucket, predicates);
>     ```
>
>   - executeBatch()
>
>     此方法用来执行批量操作。DataAbilityOperation中提供了设置操作类型、数据和操作条件的方法，开发者可自行设置自己要执行的数据库操作。插入多条数据的代码示例如下：
>
>     ```java
>     DataAbilityHelper helper = DataAbilityHelper.creator(abilityObj, insertUri);
>     // 构造批量操作
>     ValuesBucket value1 = initSingleValue();
>     DataAbilityOperation opt1 = DataAbilityOperation
>     	.newInsertBuilder(insertUri).
>         withValuesBucket(value1).
>         build();
>     ValuesBucket value2 = initSingleValue2();
>     DataAbilityOperation opt2 = DataAbilityOperation
>     	.newInsertBuilder(insertUri)
>         .withValuesBucket(value2)
>         .build();
>     ArrayList<DataAbilityOperation> operations = new ArrayList<DataAbilityOperation>();
>     operations.add(opt1);
>     operations.add(opt2);
>     DataAbilityResult[] result = helper.executeBatch(insertUri, operations);
>     ```

到这里PA & FA就基本结束了。挖的坑慢慢来吧。







## 前面说要补的坑

### 跨Ability跳转

### Data Ability里面如何写文件保存的目录

### Data Ability里关于数据库别名等的定义

### HiLog的api使用

### Math.toIntExact是个啥？

### DataAbilityHelper的Context是个啥？

### DataAbilityHelper的条件支持什么（DataAbilityPredicates APIs）

