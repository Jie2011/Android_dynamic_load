1、概念
Android 插件化 —— 是指将一个程序划分为不同的部分，比如一般 App 的皮肤样式就可以看成一个插件
Android 组件化 —— 这个概念实际跟上面相差不那么明显，组件和插件较大的区别就是：组件是指通用及复用性较高的构件，比如图片缓存就可以看成一个组件被多个 App 共用
Android 动态加载 —— 这个实际是更高层次的概念，也有叫法是热加载或 Android 动态部署，指容器（App）在运⾏状态下动态加载某个模块，从而新增功能或改变某⼀部分行为

2，开源框架
（1）开发框架
        github地址：https://github.com/singwhatiwanna/dynamic-load-apk
        详细介绍：http://www.infoq.com/cn/articles/android-dynamic-loading

我们来看下技术实现：
关于代理Activity的概念这里就不再赘述，详细请参阅dynamic-load-apk（github）[参见本文最后]。这里给一个代理Activity和插件activity的关系图。


下面主要描述下对其进行的扩展支持。先上图，如下：

1. 插件框架对于原生功能支持的实现
a）宿主App和插件的通讯方式定义
宿主App和插件的通讯方式定义使用桥接模式，分别为宿主和插件定义了接口（HostInterface，PluginInterface），各自对接口进行实现。宿主app在启动时将其接口实现set到DLlib中。插件将接口实现发布在其AndroidMainfest.xml中，并在宿主app初始化插件包时，通过反射获取该实现set到DLlib中。这样以来，两者可实现相互的界面跳转，及功能函数的调用。

b) Activity启动方式的模拟支持
Activity启动方式的模拟支持通过对代理Activity原理的了解，我们都会发现启动的插件页面实际上都是DLProxyActivity，插件activity只是这个代理Activity的一个成员对象，基于这个一对一的映射关系，我们为插件activity建立独立的task管理，通过监控DLProxyActivity的生命周期，来模拟实现插件activity的singletask，singleinstance的实现。

c) Service支持
Service支持参照代理Activity思想，同样设置代理Service。这个代理Service中维护着一个插件service的列表。每次向插件Service发送请求，实际上仍然是通过反射方式向代理Service发送请求，代理Service再进行分发。
