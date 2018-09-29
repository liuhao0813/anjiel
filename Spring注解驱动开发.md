## Spring注解驱动开发



#### @Configuration  

1、配置类 ：表示一个

@Bean  类型为返回值的类型  id默认就是方法的名称

@Bean("person")   可以通过参数指定名称

public Person person(){

return new Person("lisi",20)

}

通过如下

AnnotationConfigApplicationContext(MainConfig.class)

applicationContext.getBeanNamesForType() 按类型找到对应的Bean的名称



#### @ComponentScan

@ComponentScan()    默认value指定扫描的包，此组件是一个replicated组件，可以在一个类上指定多次，这个java8及以上版本使用，对于1.7一下可以使用@ComponentScans()的value属性指定多个值

excludeFilters=Filter[]

includeFilter=filter[]  指定扫描的时候只需要包含哪些组件，需要设置useDefaultFilters=false  因为默认是使用默认的扫描规则，如果不设置，includeFilter配置的过滤规则将不生效



@Filter 扫描规则

- FilterType.ANNOTATION   按注解的方式进行扫描过滤 最常用的一种

- FilterTyoe.ASSIGNABLE_TYPE  按指定的类型进行扫描过滤  

- FilterType.ASPECTJ  使用的是ASPECTJ表达式

- FilterType.REGIX 

- FilterType.CUSTOM 自定义规则   

  @Filter（FilterType.CUSTOM,class={MyTypeFilter.class}）

使用指定以规则需要实现TypeFilter接口， 返回false不注入类信息

metadataReader 读取到的当前正在扫描的类的信息

metadataReaderFactory  可以获取到其他任何类的信息

metadataReader .getAnnotationMetadatas()   获取当前类注解的信息

metadataReader.getResource()   获取当前类资源（类的路径）



##### 设置组件的作用域@Scope

注：spring默认的作用范围是单实例的

@Scope（）默认是单实例，主要是调整作用域

“singleton”    默认值，单实例   IOC容器启动会调用方法创建对象放到IOC容器中，以后每次获取就是直接从容器中获取

“prototype”   多实例 ,IOC容器地洞不会去调用方法创建对象放在容器中，每次获取的时候才会调用方法创建对象

“request”   同一次请求创建一个实例    这个只能在web环境中使用
“session”   同一个Session创建一个实例      这个只能在web环境中使用



#### 懒加载@Lazy

单实例bean： 默认在容器启动的时候创建对象

懒加载：容器启动不创建对象，第一次使用（获取）bean创建对象并初始化，以后每次获取都不会重新创建对象和初始化



#### @Conditional 

@Conditional   按照一定的条件进行判断，满足条件给容器中注册bean ，这个注解在spring底层会被大量使用

@Conditional(Condition[])

需要创建Condition的实现类，这个方法有两个参数

ConditionContext  判断条件使用的上下文（环境）

AnnotatedTypeMetadata 注释信息

ConfigurableListListableBeanFactory beanFactory= context.getBeanFactory()   获取IOC容器使用的BeanFactory

classLoader=context.getClassLoader();  获取类加载器

environment=contest.getEnvironment()   获取当前环境信息

registry=context.getRegistry();   获取Bean定义的注册信息

> 当@Conditional这个注解放置在类中，当满足当前条件，这个类中配置的所有Bean注册才能生效 



给容器中注册组件的方式

1. 包扫描+组件标注组件（@Controller @Service @Respsitory @Component） 自己写的类方便

2. @Bean【导入的第三方包里面的组件】

3. @Import【快速的给容器中导入一个组件】 导入组件，ID默认是组件的全类名

   -   @Import（快速的导入一个类）容器中就会自动注册这个组件，id默认是全类名

   -   ImportSelector 接口，方法返回需要导入的组件的全类名数组

     实现ImportSeletor接口，在@Import（MySelector.class）

     AnnotationMetadata 当前标注@Import注解类的所有注解信息

     > 注意：这个接口i内的方便不能返回一个NULL 的，但是可以返回一个空数组，否则会报空指针异常

   - ImportBeanDefinitionRegistrar :手动注册Bean到容器中

4. 使用Spring提供的FactoryBean（工厂Bean）

   实现FactoryBean接口，指定范型，范型就是注入的类型

   getObject就是返回的类型

   getObject返回的对象类型

   isSingleton（）返回true，表示单实例，如果是false，表示返回的多实例

   需要将自定义的FactoryBean注入到Spring容器中，这个自定义的factoryBean就是主要注入的对象，也就是工厂Bean调用getObject创建的对象

   默认获取到的工厂Bean调用getObject

   如果想获取到自定义factoryBean的真实对象，可以在获取bean的ID前增加&

   &colorFactoryBean



#### Bean的生命周期

Bean的生命周期

​     Bean创建---->初始化----销毁的过程

容器管理bean的生命周期

自定义初始化和销毁方法

  构造器

​	单实例，在容器启动的时候创建对象

​        多实例：在每次获取的时候创建对象

初始化

​     对象创建完成，并赋值好，调用初始化方法  如果是多实例的情况下在获取的时候进行初始化

销毁

​	单实例：在容器关闭的时候进行销毁

​        多实例：容器不会管理这个Bean，容器不会调用销毁方法

1. 指定初始化和销毁  init-method 和destroy-method









