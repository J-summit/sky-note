### 1.spring IOC

#### 1).理解

(Inversion of control,即控制反转),其实只是一个容器，用于存储bean，将初始化好的bean(单例)交给容器控制(bean之间的创建 装配 销毁整个生命周期)，并不是直接对对象直接控制，是spring核心

a.IOC与DI

IOC是一种思想，而DI(依赖注入)把符合依赖关系的对象传递给需要的对象，

方式: setter注入、构造器注入、接口注入

b.两种Ioc容器

BeanFactory,ApplicationContext

1)BeanFactory：Bean集合的工厂类，

2)ApplicationContext：对于BeanFactory扩展，新增了

- MessageSource ：管理 message ，实现国际化等功能。
- ApplicationEventPublisher ：事件发布。
- ResourcePatternResolver ：多资源加载。
- EnvironmentCapable ：系统 Environment（profile + Properties）相关。
- Lifecycle ：管理生命周期。
- Closable ：关闭，释放资源
- InitializingBean：自定义初始化。
- BeanNameAware：设置 beanName 的 Aware 接口。

### 3.spring 事务
