# Angular题目汇总

## 高频题目

1. 装饰器有哪些
2. 指令有哪些
3. service用来干什么的
4. 依赖注入是什么
5. angular的生命周期有哪些
6. angular和vue的区别
7. rxjs是什么？基本概念有哪些
8. rxjs常用的api

## 非高频题目

1. module是什么
2. 管道的作用
3. 数据之间通信有哪些方式
4. angular优缺点
5. angular执行变更检测的情况
6. 数据绑定的方式有哪些
7. zone是什么
8. AOT和JIT
9. router和route
10. promise和observable的区别

## 高频题目答案

### 装饰器有哪些

1. component
2. injectable
3. directive
4. pipe
5. hostlistener
6. hostbinding
7. input/output
8. ngmodule

### 指令有哪些

**结构型指令：** 通过添加或删除dom元素更改dom布局的指令

ngfor

ngif

ngswitch

**属性型指令：**

ngclass

ngstyle

ngmodel



### service用来干什么的

1. 跨层级通信、存储数据
2. 面向切面编程（AOP的概念）

AOP：把跟核心业务无关的功能抽离出来，再通过动态织入的方式渗入业务逻辑中

好处：保持业务逻辑模块组件内的纯净和高内聚，其次还可以更方便的进行复用



### 依赖注入是什么

**定义：**

Dependency Injection（DI）是一种设计模式，在这种设计模式中，类会从外部源请求依赖项而不是创建它们。**依赖注入是控制反转的具体实现方式**。拿ng举例来说，在一个组件中会去请求一个商品类型的service，而不需要去创建一个商品。我也不需要知道我要怎么做才能去创建，我只要请求该依赖就好，将其注入在构造器中就好。

**优点：**

- 松耦合，增加了代码的可扩展性
- 使代码可重用性更高

**如何做到这两个优点的？举个例子**

当我们创建一个配送订单时，我们需要创建商品这个对象，它是锅碗瓢盆还是说是高贵的数码产品，假如我们此时直接写代码的话

可能会写createShipment(guoProduct, 配送地址， 配送公司)，配送地址可能又是通过一系列操作得到的，当我们换成createShipment(techologyProduct, 配送地址， 配送公司)，我们此时又需要改一大堆代码，这就是紧耦合带来的坏处，并且不利于复用和可维护。

而当我们使用依赖注入时，我们只需要请求锅碗瓢盆这个类，里面做了什么跟我没关系，我不用知道，这样创建一个订单，就相当于是依赖注入。而当换成科技产品时，我们也只需要去请求锅碗瓢盆这个类给一个商品类型即可，不用关心别的操作



**控制反转定义（IOC）**

控制什么？：控制外部的一个资源获取

反转是什么？：那我们需要知道什么是正转，正转就是通过new主动去创建一个对象。反转：由反转容器去创建控制对象，将控制权从代码内部转入代码外部，这就是控制反转。控制反转的手段叫做依赖注入，控制反转是目的。

传统应用程序都是由我们在类内部主动创建依赖对象，也就是所谓的new。从而导致类与类之间高耦合，难于测试；有了IoC容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是 松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活，实现控制反转的容器叫做ioc容器，angular就是ioc容器

什么叫做代码内部转入代码外部，就像上述例子，我控制权不在创建配送订单时，配送订单只是我使用实例化对象的一个操作，而在代码外部也就是产品中进行一系列操作



### angular的生命周期有哪些

1. ngonchanges
2. ngoninit
3. ngDoCheck
4. ngAfterContentInit
5. ngAfterContentChecked
6. ngAfterViewInit
7. ngAfterViewChecked
8. ngOnDestroy

### angular和vue的区别

- vue和angular都可以进行动态绑定，例如动态绑定样式，绑定数据，条件式渲染等。vue中的动态绑定都以v开头，而angular以ng开头。
- Vue中冒号相当于angular中的中括号，可以单向绑定动态的值,
- angular中的属性型指令，ngclass,ngstyle,对应到vue中就是:class,  :style,ngif, ngswitchcase, v-if v-else-if
- 都有依赖注入的概念，angular是注入service，注入的是功能所需的服务或者对象，此时组件会从外部源请求依赖项而不是创建他们，service中一般都会存放一些抽离于视图，只处理业务的一些通用逻辑，通过provider提供，直接注入进构造器中。而vue是为了解决prop逐级透传问题而提出的依赖注入，
- 都是以html为模板，而不是像react一样嵌套着js写，不同：angular是html，css，ts解耦的写法，也就是数据，视图，样式分离的写法，而vue是单文件类型的写法
- angular的写法相当于vue3中的组合式api，因为方法，属性等都集中在一起，不需要像vue2的选项式api一样反复来回查看不同地方的代码，因此研发效率高，
- angular中ts是必选项，而vue中是可选项

### rxjs是什么？基本概念有哪些

**定义：**

用于 JavaScript 的 ReactiveX 库

**Observable：** 相当于是数据源，一个可观察对象

**Subscribe：** 相当于去订阅去监听这个流的流动

**Observer：** 观察者，相当于一个哨兵，推送通知的消费者的接口，里面有next，error，complete，（单播，只会推送一次）

**Subscription：**就是表示**Observable**的执行，可以被清理。这个对象最常用的方法就是**unsubscribe**方法，它不需要任何参数，只是用来清理由**Subscription**占用的资源。

**subject：** 相当于一个大喇叭，既是一个 Observable 又是一个 Observer**,**多播（一次执行时向多个订阅者列表进行广播）给被观察者observer



### rxjs常用的api

from/interval/of/timer

concat/merge/race/debouncetime/throttletime/switchmap/forkjon/catcherror/finally/tap

first/last/skip/skipwhile

## 非高频题目答案

### module是什么

定义：相当于应用程序中的边界，用来分离应用程序之间的功能。

imports属性告诉angular需要导入加载哪些模块

declarations属性用于声明本模块中拥有的视图类，Angular有三种视图类：组件、指令和管道

providers表明该模块可以提供的服务，管道等

bootstrap当这个模块被引导时被引导的一组组件。这里列出的组件会自动添加到entryComponents中。告诉angular根组件是什么

entrycomponent：

定义要编译的组件集，这样它们才可以动态加载到视图中

### 管道的作用

定义： 管道接收数据作为输入，并将其转换为所需的输出。例如，让我们使用一个管道，使用日期管道将组件的生日属性转换为人类友好的日期。

### 数据之间通信有哪些方式

- 使用事件
- 使用服务
- 使用继承
- 使用rxjs

### angular优缺点

**优点：**

1. 数据双向绑定
2. 添加自定义的指令、管道
3. 支持表单验证
4. 支持依赖注入
5. 强大的表单功能

**缺点：**

1. 太过笨重
2. 编译速度慢

### angular执行变更检测的情况

-  网络请求,
-  异步操作, 
- Input&Output

### 数据绑定的方式有哪些

- 字符串插值（花括号绑定）
- 属性绑定（单向绑定）
- 事件绑定（output事件绑定）
- 双向数据绑定（相当于属性绑定和事件绑定结合的语法糖）

### zone是什么

Zone 是跨异步任务持续存在的执行上下文。当本机 JavaScript 操作引发事件时，Angular 依赖于 zone.js 来运行 Angular 的变更检测 

### AOT和JIT

**AOT**：ahead of time预编译 

**JIT**： just in time即时编译

在Angular 8之前，JIT编译是默认的，现在默认是AOT。当你运行ng build(只构建)或ng serve(本地构建和服务)CLI命令时，编译的类型(JIT或AOT)取决于angular.json中构建配置中指定的AOT属性的值。缺省情况下，aot为true。

**AOT**的好处：

- 更快的渲染速度：浏览器下载应用程序的预编译版本。因此它可以立即呈现应用程序而无需编译应用程序。
- 更小的下载体积：不需要下载     Angular 编译器。因此，它大大减少了应用程序的有效负载。
- 更早的检测模板错误
- 更加安全：它将 HTML 模板和组件编译成     JavaScript。所以不会有任何注入攻击。

### router和route

**router：** 从一个页面跳转到另一个页面的机制，

里面有router-link、router-outlet

**route：** 一个路由器，定义了路由跳转规则

路由器必须配置有路由定义列表。您通过 RouterModule.forRoot() 方法使用路由配置路由器，并将结果添加到 AppModule 的导入数组

### promise和observable的区别

| observable                                             | promise               |
| ------------------------------------------------------ | --------------------- |
| 计算在订阅之前不会开始，因此它们可以在您需要结果时运行 | 在创建时立刻执行      |
| 随着时间的推移可以有多个值                             | 只能有一个值          |
| 订阅方法中可以进行错误处理，使错误处理集中且可预测     | 将错误推送给子promise |
| 提供丰富的api去处理异步程序                            | api较少               |

