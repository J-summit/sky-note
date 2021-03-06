[TOC]
### 1.spring IOC 容器获取
底层是一个map，bean的注册、实例化、管理
#### 1).ClassPathXmlApplicationContext
[代码演示](#1.ClassPathXmlApplicationContext)

#### 2).AnnotationConfigApplicationContext

[代码演示](#2.AnnotationConfigApplicationContext)

##### a.@Configuration
spring3.0声明配置类，取代applicationContext.xml 配置文件
##### b.@Bean
将对象注入到spring中,bean的id默认取方法名
###### 1).@Lazy
懒人式加载，需要的时候才会初始化
###### 2). @Scope
设置bean的作用域
bean作用 默认singleton，其它为prototype，request，session，application,GlobalSession
[见](#2.AnnotationConfigApplicationContext)

##### c.@ComponentScan
组件扫描，
- 可以指定范围，value指定哪个包下
- 扫描过滤
- 自定义过滤
[见](#2.AnnotationConfigApplicationContext)
##### d.@Component
$$
@Component组件\left\{
\begin{aligned}
@Repository& 持久化层 \\
@Service& 业务层\\
@Controller& 控制层 \\
\end{aligned}
\right.
$$
**都是声明为bean,其实三个注解类上多有@Component，相当于其子类**
##### e.@Conditional
与@Bean连用，满足特定的条件,该bean才会被注入
[代码演示](#3.@Conditional演示demo)
##### f.@Import
也是往容器中注入bean,默认全类名（bean的id）
- 直接指定value穿bean的类

- 实现ImportSelector接口，返回全类名数组

- 实现 ImportBeanDefinitionRegistrar接口
  [代码演示](#4.@Import代码)
##### g.FactoryBean接口
也是将该类注入到容器中
  [代码演示](#5.FactoryBean接口代码)

#### 7.@Qualifier @PriMary

都是用于bean的注入，@Qualifier根据id查找bean，@PriMary，注入是优先注入被该注解修饰的

### 代码演示
#### 1.ClassPathXmlApplicationContext
1.实体类
```
/**
 * @author summit
 * @since 2019/9/23 22:34
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class User {

    private int id;

    private String name;
}
```
2.bean.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<beans   xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    <bean id="user"  class="cn.summit.entity.User">
        <property name="id" value="1"/>
        <property name="name" value="summit"/>
    </bean>
</beans>
```
3.测试类
```java
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml");
        User user = (User)applicationContext.getBean("user");
        System.out.println(user);
        String[] beanDefinitionNames = applicationContext.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            System.out.println(beanDefinitionName);
        }
    }
```
运行结果
```
User(id=1, name=summit)
user
```
#### 2.AnnotationConfigApplicationContext

##### 2.1 Configuration类
```
/**
 * @author summit
 * @since 2019/10/13 14:06
 */
@Configuration
//@ComponentScan(value = "cn.summit.entity.IOCget", excludeFilters = {
//        @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Summit.class})
//}, useDefaultFilters = true)
@ComponentScan(value = "cn.summit.entity.IOCget", excludeFilters = {
       @ComponentScan.Filter(type = FilterType.CUSTOM, classes = {MyTypeFiler.class})
}, useDefaultFilters = true)
public class ConfigurationTest {

    @Bean
    @Lazy
    public User user() {
        System.out.println("@bean");
        return new User(2, "sky");
    }
}
```
##### 2.2 MyTypeFiler
```
public class MyTypeFiler implements TypeFilter {

    /**
     *
     * @param metadataReader 当前扫描类型信息
     * @param metadataReaderFactory 其他任何类信息
     * @return
     * @throws IOException
     */
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        //注解信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        //类路径
        Resource resource = metadataReader.getResource();
        System.out.println(resource.getDescription());
        //类信息
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        System.out.println(classMetadata.getClassName());
        return false;
    }
}
```
##### 2.3 component
```
@Repository
public class UserDao {
}

@Service
public class UserService {
}

@Controller
@Summit
public class UserController {
}

public @interface Summit {
}
```
##### 2.4 MyTypeFiler
```
public class MyTypeFiler implements TypeFilter {

    /**
     *
     * @param metadataReader 当前扫描类型信息
     * @param metadataReaderFactory 其他任何类信息
     * @return
     * @throws IOException
     */
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        //注解信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        //类路径
        Resource resource = metadataReader.getResource();
        System.out.println(resource.getDescription());
        //类信息
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        System.out.println(classMetadata.getClassName());
        return false;
    }
}
```
##### 2.5 test
```
/**
 * @author summit
 * @since 2019/10/13 14:04
 */
public class AnnotationConfigApplicationContextTest {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(ConfigurationTest.class);
        System.out.println("IOC 初始化");
        User user = (User)applicationContext.getBean("user");
        Arrays.stream(applicationContext.getBeanDefinitionNames()).forEach(System.out::println);
        System.out.println(user);
    }
}
```
##### 2.6 结果
```
file [D:\onlyoproject\mysky\enjoy-study\spring-study\target\classes\cn\summit\entity\IOCget\AnnotationConfigApplicationContextTest.class]
cn.summit.entity.IOCget.AnnotationConfigApplicationContextTest
file [D:\onlyoproject\mysky\enjoy-study\spring-study\target\classes\cn\summit\entity\IOCget\ClassPatchApplicationTest.class]
cn.summit.entity.IOCget.ClassPatchApplicationTest
file [D:\onlyoproject\mysky\enjoy-study\spring-study\target\classes\cn\summit\entity\IOCget\controller\UserController.class]
cn.summit.entity.IOCget.controller.UserController
file [D:\onlyoproject\mysky\enjoy-study\spring-study\target\classes\cn\summit\entity\IOCget\Dao\UserDao.class]
cn.summit.entity.IOCget.Dao.UserDao
file [D:\onlyoproject\mysky\enjoy-study\spring-study\target\classes\cn\summit\entity\IOCget\myannotation\Summit.class]
cn.summit.entity.IOCget.myannotation.Summit
file [D:\onlyoproject\mysky\enjoy-study\spring-study\target\classes\cn\summit\entity\IOCget\MyTypeFiler.class]
cn.summit.entity.IOCget.MyTypeFiler
file [D:\onlyoproject\mysky\enjoy-study\spring-study\target\classes\cn\summit\entity\IOCget\service\UserService.class]
cn.summit.entity.IOCget.service.UserService
IOC 初始化
@bean
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
configurationTest
userController
userDao
userService
user
User(id=2, name=sky)

```
#### 3.@Conditional演示demo
##### 3.1 配置类
```java
@Configuration
public class TestConfig {

    @Bean("window")
    @Conditional(DevCondition.class)
    public UserBean window() {
        System.out.println("注入 window");
        return new UserBean();
    }
}
```
##### 3.2 Condition类
```
/**
 * @author summit
 * @since 2019/9/25 21:32
 */
public class DevCondition implements Condition {

    /**
     * 开发环境使用的是 window系统
     * 实现Condition接口自定义注入条件
     *
     * @param context
     * @param metadata
     * @return
     */
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        Environment environment = context.getEnvironment();
        String property = environment.getProperty("os.name");
        System.out.println("当前操作系统"+property);
        if (property.contains("Window")) {
            return true;
        }
        return false;
    }
}
```
##### 3.3 测试结果
在windows系统下注入，在linux不注入

#### 4.@Import代码
##### 4.1 configuation类
```
@Configuration
@Import(value = {MyBeanDefinitionRegist.class,T3Entity1.class,T3Entity2.class,ImportSelectorTest.class})
public class Test3Config {

    @Bean
    public MyFactoryBean myFactoryBean() {
        return new MyFactoryBean();
    }
}
```
##### 4.2 ImportSelector接口实现
```
/**
 * @author summit
 * @since 2019/9/27 22:38
 */
public class ImportSelectorTest implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"cn.summit.entity.springconfig.Test3.E3Entity3","cn.summit.entity.springconfig.Test3.E3Entity4"};
    }
}

```
##### 4.3 ImportBeanDefinitionRegistrar接口实现
```
public class MyBeanDefinitionRegist implements ImportBeanDefinitionRegistrar {
    /**
     * 注册bean到ioc
     *
     * @param importingClassMetadata 注解
     * @param registry 注册
     */
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        System.out.println(Thread.currentThread().getName());
        boolean b = registry.containsBeanDefinition("cn.summit.entity.springconfig.Test3.T3Entity2");
        System.out.println(b);
        String beanname = "registerEntity-test";
        RootBeanDefinition beanDefinition = new RootBeanDefinition(RegisterEntity.class);
        registry.registerBeanDefinition(beanname,beanDefinition);

    }
}
```
##### 4.4 测试
```
   public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(Test3Config.class);
        String[] beanDefinitionNames = applicationContext.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            System.out.println(beanDefinitionName);
        }
    }
```

#### 5.FactoryBean接口代码

```
public class MyFactoryBean implements FactoryBean<T3Entity5> {

    @Override
    public T3Entity5 getObject() throws Exception {
        return new T3Entity5();
    }

    @Override
    public Class<?> getObjectType() {
        return T3Entity5.class;
    }
}

```
```
    @Bean
    public MyFactoryBean myFactoryBean() {
        return new MyFactoryBean();
    }
```




