[TOC]
### 1.spring 

#### spring七大核心
1)spring core:即spring核心，它是框架最基础的部分，提供IOC和依赖注入特性
2)spring context:spring上下文容器，它是BeanFactory功能加强的一个子接口
3)spirng web：它是提供web应用开发支持
4)spring mvc:它是针对web应用MVC思想实现
5)spring dao：提供对JDBC抽象层，简化JDBC编码
6)spring orm：流行的ORM框架整合
7)spirng aop：面向切面编程



### spring
#### 1.FactoryBean，BeanFactory
1)把我们java实例Bean通过FactoryBean注入到容器中
2)BeanFactory 从我们容器中获取实例Bean

### 1.bean的生命周期

创建->初始化->销毁

## 3.spring三级缓存问题（循环依赖）



A中依赖B 然后又在B中注入A，平时使用也十分常见，说明spring已经帮我们解决了

首先要知道**引用传递**的概念，spring就用运用引用传递，当获得该对象的引用时，对象的属性是可以延后设值

从bean的生命周期看

开始->bean实例化—》 set bean属性 -》看是否实现Aware接口-》前置处理器....

循环依赖的问题发生在set bean属性时，源码分析

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry

```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {
	//从上至下 分表代表这“三级缓存
	/** bean实例对象(完全初始化好的) */
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	/** 单例对象工厂cache */
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	/** 早期bean实例对象（也就是set属性前的bean） */
	private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
```

```
	/**
	 * Return the (raw) singleton object registered under the given name.
	 * <p>Checks already instantiated singletons and also allows for an early
	 * reference to a currently created singleton (resolving a circular reference).
	 * @param beanName the name of the bean to look for
	 * @param allowEarlyReference whether early references should be created or not
	 * @return the registered singleton object, or {@code null} if none found
	 */
	@Nullable
	protected Object getSingleton(String beanName, boolean allowEarlyReference) {
		Object singletonObject = this.singletonObjects.get(beanName);
		if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
			synchronized (this.singletonObjects) {
				singletonObject = this.earlySingletonObjects.get(beanName);
				if (singletonObject == null && allowEarlyReference) {
					ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
					if (singletonFactory != null) {
						singletonObject = singletonFactory.getObject();
						this.earlySingletonObjects.put(beanName, singletonObject);
						this.singletonFactories.remove(beanName);
					}
				}
			}
		}
		return singletonObject;
	}
```

从上面分析，getBean先从singletonObjects中获取，没则从earlySingletonObjects获取没则从singletonFactories，如果获取到了就从singletonFactories中移除，并且放进**`**earlySingletonObjects**`**。其实也就是从三级缓存**`**移动（是剪切、不是复制哦~）**`**到了二级缓存