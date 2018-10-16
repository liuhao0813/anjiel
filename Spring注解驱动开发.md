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

   ​      registerBeanDefinition()

   AnnotationMetadata 当前类的注解信息 

   BeanDefinitionRegistry    BeanDefinition注册类

   把所有需要添加到容器中的bean：调用 BeanDefinitionRegistry.registerBeanDefinition手工注册进来

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

2.  通过让Bean实现InitializingBean 接口，初始化Bean

   通过让Bean实现DisposableBean接口 ，销毁Bean

3. 可以使用JSR250规范中定义的注解来控制Bean的初始化和销毁

   @PostContructhe 在bean船舰完成并且属性赋值完成，来下执行初始化方法

   @PreDestroy   当Bean从容器中移除之前通知我们进行清理工作

4. BeanPostProcessor:Bean的猴子处理器

   在Bean初始化前后进行的一些处理工作

   postProcessBeforeInitialization  在Bean初始化之前工作

   两个参数，Object 新创建的Bean实例，BeanName就是在容器中Bean的名称

   postProcessAfterInitialization:在Bean初始化之后工作



#### 属性赋值

1. @Value

   - 基本素质
   - 可以写SpEL表达式：#{}
   - 可以写${} 取出配置文件中的之（在运行环境变量里面的值）

2. @PropertySource加载外部配置文件

   @PropertySource(value={classpath：/person.properties})   读取外部配置文件中的K/V保存到环境变量中，加载完外部配置文件后，使用${}取出配置文件中的值   方法读取classpath下的person.properties文件

3. 自动装配  

    spring利用依赖注入（DI） 完成对IOC容器中各个组件的依赖关系赋值

   - Autowired：自动注入 

     1. 默认按照Bean的类型去容器中找对应的组件，applicationContext.getBean(BookDao.class)

     2. 如果找到多个相同类型的组件，再将属性的名称作为组件的ID去容器中查找

     3. @Qualifier  使用@Qualifier注解指定需要装配组件的ID，而不是使用属性名

     4. 自动装配，默认一定要将属性赋值好，没有就会报错

        可以使用@Autowired（required=false） 

     5. @Primary  ：让spring进行自动装配的时候，默认使用首选的Bean

        也可以继续使用@Qualifier指定需要装配的Bean的名字

   - spring还支持使用@Resource（JSR250）和@Injeict（JSR330）[JAVA规范]

     1. @Resource：

        可以和@Autowired一样实现自动装配，默认是按照组件名称进行装配的

        没有能支持@Primary功能没有支持@Autowired（required=false）

     2. @Inject

        需要导入javax.inject包，和autowired的功能一样，没有required=false的功能

     @Autowired：spring定义的，@Resource，@Inject都是java规范

     AutowiredAnnoationBeanPostProcessor   通过这个后置处理实现的  解析完成自动装配的功能

   - @Autowired  ：构造器，参数，方法，属性  都是从容器中获取值

     1. 标注的方法   @Bean +方法参数，参数从容器中获取；默认不写@Autowired效果是一样的；都能自动装配

        @Bean标注的方法创建对象的时候，方法参数的值从容器中获取

        标注在方法，SPring容器创建当前对象，就会调用方法，完成赋值

        方法使用的参数，自定义类型的值从IOC容器中获取

        默认加在ioc容器中的组件，容器启动会调用无参构造器创建对象，再进行初始化赋值等操作

     2. 标注在有参构造器上   如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略，参数位置的组件还是可以自动从容器中获取

        构造器要用的参数，也会从容器中获取

     3. 放在参数位置

     参数也会从容器中获取

   - 自定义组件想要使用spring容器底层的一些组件

     自定义组件实现ＸＸＸＡｗａｒｅ接口，在创建对象的时候，会调用接口规定的方法注入相关组件，Ａｗａｒｅ；

     把Spring底层一些组件注入到自定义的Bean中；

     xxxAware：功能使用xxxProcessor；

     ​      ApplicationContextAware=》ApplicationContextAwareProcessor

     ​       

   - Profile: 指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件

     ​	spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能

     1. 加了环境标识的Bean，只有这个环境被激活的时候才能注册到容器中， 默认是default环境

        -  使用命令行动态参数   -Dspring.profiles.active=test  在虚拟机参数位置加

        - 使用代码的方式激活

          ​	使用无参构造器的方式，AnnotationConfigApplicationContext  

          ​	applicationContext.setEnviroment().setActiveProfiles()  指定激活的配置文件

     2. 写在配置类上，只有是指定的环境的时候，整个配置类里面的的所有配置才能开始生效

     3. 没有标注环境标识的bean，在任何环境下都是加载的











## AOP[动态代理]

​	指在程序运行期间动态的将某段代码切入到指定的方法位置进行运行的编程方式

1. 导入Aop模块，SPring AOP（spring-aspect）

2. 定义一个业务逻辑类，MathCalculator ，在业务逻辑运行的时候将日志进行打印

3. 定义一个日志切面类，LogAspects ，切面类里面的方法需要动态感知Mathcalculator.div运行到

   通知方法：

   - 前置通知@Before：在目标方法运行之前运行
   - 后置通知@After：在牧方法运行结束之后运行（无论方法的正常结束还是异常结束）
   - 返回通知@AfterReturning：在目标方法正常返回之后运行
   - 异常通知@AfterThrowing：在目标方法出现异常以后运行
   - 环绕通知@Around： 动态代理，手动推进目标方法运行（JoinPoint.proceed()）

4. 给切面类的目标方法标注合适何地运行（通知注解）

   通过@Pointcut抽取公共的切入点表达式

   1. 本类引用，  point Cut()
   2. 其他的漆面引用 execution()

5. 将切面类和业务逻辑类（目标方法所在的类）都加入到容器中

6. 必须告诉Spring哪个类是切面类，给切面类上加一个注解：（@Aspect） 告诉Spring当前类是一个切面类

7. 给配置类中加@EnableAspectJAutoPorxy，开启基于注解的AOP模式

   在Spring中很多都是通过@EnableXXXXX

   JoinPoint一定要出现在参数表中的第一个参数，否则spring无法识别，会报错



三步：

1. 将业务逻辑组件和切面类都加入到容器中，告诉spring那个是切面类（@Aspect）
2. 在切面类上的每个通知方法上标注通知注解，告诉spring何时何地的运行（切入点表达式）
3. 开启基于注解的Aop模式，@EnableAspectJAutoProxy  



AOP原理

​	@EnableAspectJAutoProxy

1. @EnableAspectJAutoProxy是什么?
2. 
3. 

