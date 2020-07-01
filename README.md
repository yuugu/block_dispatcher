# block_dispatcher

#模块化开发中的模块生命周期管理及模块间相互调用

##要解决的问题

- 从模块化的定义中可以看出，模块化的核心在于**解耦**与**拆分代码**。
- 进行工程拆分之后，模块之间是**不应该**存在直接相互依赖的，否则由于模块之间的直接依赖，会导致两个模块直接耦合过深，是达不到解耦的目的的。

- **模块交互：** 拆分之后，既然**不允许模块之间相互依赖**（例如`implementation project(':module-another')`），那么模块之间如何进行交互呢？
- **生命周期：** 每个模块最终会被集成到主工程，那就意味着模块不能拥有自己的`Application`并在其中执行一些初始化逻辑。那么，生命周期应该如何管理？

##为解决上面的问题，本框架来支持模块化开发中的模块生命周期管理及模块间协作。

###首先定义两个概念：

- **模块(`Module`)：** 模块化中单个业务模块或者功能模块。在本框架中使用接口`Module`来进行抽象描述。
- **服务(`Service`)：** 指的是上述模块对外暴露的**接口**。每个模块将在`Module`接口的实现类中声明自己提供的服务。

###本框架主要功能如下：

**模块生命周期管理**
我们抽象出`Module`接口来描述模块，内部包含`attach(Context)`与`detach()`两个生命周期方法，分别在模块被注册与反注册时回调。

**支持模块间交互：面向接口编程**
由模块对外提供接口，其他模块根据提供的接口来调用当前模块的服务，达到面向接口编程的目的。其中，其他模块会通过本框架获取接口的实例。

**模块的注册与反注册**
支持模块在运行时进行动态注册与反注册。

**模块运行时依赖关系管理**
模块之间是存在依赖关系的，一些模块可能需要等待另一个模块被加载后才开始加载自己，这一场景在启动流程中尤为常见。
因此，本框架允许模块在`Module`接口的`dependsOn`方法中声明自己对别的模块的运行时依赖。
在模块的注册过程中，本框架会根据每个模块的`dependsOn`方法提供的依赖关系，自动梳理出一个合理的注册顺序，确保每个模块被加载时，其依赖的模块已经被加载。

