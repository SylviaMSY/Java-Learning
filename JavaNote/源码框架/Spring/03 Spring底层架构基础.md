

## BeanDefinition

1. BeanDefinition表示Bean的定义，有很多属性用来描述一个Bean的特点：

   - class：表示Bean的类型
   - scope：表示Bean的作用域，单例或原型（多例）
   - lazylnit：表示Bean是否是懒加载
   - initMethodName：表示Bean初始化时要执行的方法
   - destroyMethodName：表示Bean销毁时要执行的方法

2. 定义方式：

   - 声明式定义：(最终会被Spring解析为对应的BeanDefinition对象，并放入Spring容器中)

     - <bean/>
     - @Bean
     - @Component(@Service, @Controller)

   - 编程式定义（通过BeanDefinition）

     ``` java
     AnnotationConfigApplicationContext context = new
     AnnotationConfigApplicationContext(AppConfig.class);
     // 生成一个BeanDefinition对象，并设置beanClass为User.class，并注册到ApplicationContext中
     AbstractBeanDefinition beanDefinition =
     BeanDefinitionBuilder.genericBeanDefinition().getBeanDefinition();
     beanDefinition.setBeanClass(User.class);
     context.registerBeanDefinition("user", beanDefinition);
     System.out.println(context.getBean("user"));
     ```

     

## BeanDefinitionReader

- #### AnnotatedBeanDefinitionReader

  > 可以直接把某个类转换为BeanDefinition，并且会解析该类上的注解（@Conditional, @Scope, @Lazy, @Primary, @DependsOn, @Role, @Description）

  ``` java
  AnnotationConfigApplicationContext context = new
  AnnotationConfigApplicationContext(AppConfig.class);
  AnnotatedBeanDefinitionReader annotatedBeanDefinitionReader = new
  AnnotatedBeanDefinitionReader(context);
  // 将User.class解析为BeanDefinition
  annotatedBeanDefinitionReader.register(User.class);
  System.out.println(context.getBean("user"));
  ```

- #### XMLBeanDefinitionReader

  > 可以解析<Bean>标签

  ``` java
  AnnotationConfigApplicationContext context = new
  AnnotationConfigApplicationContext(AppConfig.class);
  XmlBeanDefinitionReader xmlBeanDefinitionReader = new XmlBeanDefinitionReader(context);
  int i = xmlBeanDefinitionReader.loadBeanDefinitions("spring.xml");
  System.out.println(context.getBean("user"));
  
  ```

- #### ClassPathBeanDefinitionScanner

  > 扫描器，作用和Reader类似，对包路径下扫描到的类进行解析，存在@Component注解，就会解析成一个BeanDefinition

  ``` java
  AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();
  context.refresh();
  ClassPathBeanDefinitionScanner scanner = new ClassPathBeanDefinitionScanner(context);
  scanner.scan("com.zhouyu");
  System.out.println(context.getBean("userService"));
  ```



## BeanFactory

- #### BeanFactory 接口

  > 表示Bean工厂，负责创建Bean，并提供获取Bean的API

- #### ApplicationContext 接口

  > ApplicationContext是BeanFactory的一种

  ``` java
  public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory,
  HierarchicalBeanFactory,
  MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
  ...
  }
  ```

  当new一个ApplicationContext时，Spring底层会new一个BeanFactory，当使用ApplicationContext的某些方法时，例如getBean()，底层调用的是BeanFactory的getBean()方法

- #### DefaultListableBeanFactory 类

  > BeanFactory接口的实现类之一

  ``` java
  DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
  AbstractBeanDefinition beanDefinition =
  BeanDefinitionBuilder.genericBeanDefinition().getBeanDefinition();
  beanDefinition.setBeanClass(User.class);
  beanFactory.registerBeanDefinition("user", beanDefinition);
  System.out.println(beanFactory.getBean("user"));
  ```

  DefaultListableBeanFactory实现了很多接口：

  1. AliasRegistry：支持别名
  2. BeanDefinitionRegistry：可以注册、保存、移除、获取某个BeanDefinition
  3. BeanFactory：Bean工厂，根据bean名字、类型、或别名获取某个Bean对象
  4. SingletonBeanFactory：可以直接注册、获取某个单例Bean
  5. SimpleAliasRegistry：是一个类，实现了AliasRegistry接口，支持别名
  6. ListableBeanFactory：在BeanFactory增加了其他功能，可获取所有的beanNames
  7. HierarchicalBeanFactory：在BeanFactory上增加了获取父BeanFactory功能
  8. DefaultSingletonBeanRegistry：是一个类，实现SingletonBeanFactory接口，直接注册、获取某个单例Bean功能
  9. ConfigurableBeanFactory：在HierarchicalBeanFactory和SingletonBeanRegistry的基础上， 添加了设置父BeanFactory、类加载器（表示可以指定某个类加载器进行类的加载）、设置 Spring EL表达式解析器（表示该BeanFactory可以解析EL表达式）、设置类型转化服务（表示 该BeanFactory可以进行类型转化）、可以添加BeanPostProcessor（表示该BeanFactory支持 Bean的后置处理器），可以合并BeanDefinition，可以销毁某个Bean等等功能
  10. FactoryBeanRegistrySupport：支持FactoryBean功能
  11. AutowireCapableBeanFactory：支持在创建Bean的过程中能对Bean进行自动装配
  12. AbstractBeanFactory：实现了ConfigurableBeanFactory接口，继承了 FactoryBeanRegistrySupport，这个BeanFactory的功能已经很全面了，但是不能自动装配和 获取beanNames
  13. ConfigurablelistableBeanFactory
  14. AbstractAutowireCapableBeanFactory
  15. DefaultListableBeanFactory



## ApplicationContext

- #### 是个接口，也是一个BeanFactory。具有以下功能

  1. HierarchicalBeanFactory：获取父Bean
  2. ListableBeanFactory：获取beanNames
  3. ResourcePatternResolver：资源加载器，可一次性获取多个资源
  4. EnvironmentCapable：可获取运行时环境
  5. ApplicationEventPublisher：拥有广播事件功能
  6. MessageSource：国际化功能

- #### AnnotationConfigApplicationContext 实现类

  1. ConfigurableApplicationContext
  2. AbstractApplicationContext
  3. GenericApplicationContext
  4. AnnotationConfigRegistry
  5. AnnotationConfigApplicationContext

- #### ClassPathXmlApplicationContext

  1. 国际化

     ``` java
     @Bean
     public MessageSource messageSource() {
     ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
     messageSource.setBasename("messages");
         return messageSource;
     }
     context.getMessage("test", null, new Locale("en_CN"))
     ```

  2. 资源加载

     ``` java
     AnnotationConfigApplicationContext context = new
     AnnotationConfigApplicationContext(AppConfig.class);
     Resource resource = context.getResource("file://D:\\IdeaProjects\\spring‐
     framework\\luban\\src\\main\\java\\com\\luban\\entity\\User.java");
     System.out.println(resource.contentLength());
                                             
     Resource[] resources = context.getResources("classpath:com/zhouyu/*.class");
     for (Resource resource : resources) {
         System.out.println(resource.contentLength());
         System.out.println(resource.getFilename());
     }                                   
     ```

  3. 获取运行时环境

     ``` java
     AnnotationConfigApplicationContext context = new
     AnnotationConfigApplicationContext(AppConfig.class);
     Map<String, Object> systemEnvironment = context.getEnvironment().getSystemEnvironment();
     System.out.println(systemEnvironment);
     System.out.println("=======");
     Map<String, Object> systemProperties = context.getEnvironment().getSystemProperties();
     System.out.println(systemProperties);
     System.out.println("=======");
     MutablePropertySources propertySources = context.getEnvironment().getPropertySources();
     System.out.println(propertySources);
     System.out.println("=======");
     System.out.println(context.getEnvironment().getProperty("NO_PROXY"));
     System.out.println(context.getEnvironment().getProperty("sun.jnu.encoding"));
     System.out.println(context.getEnvironment().getProperty("zhouyu"));
     ```

  4. 事件发布

     ``` java
     @Bean
     public ApplicationListener applicationListener() {
         return new ApplicationListener() {
             @Override
             public void onApplicationEvent(ApplicationEvent event) {
                 System.out.println("接收到了一个事件");
             }
         };
     }
     context.publishEvent("kkk");//发布一个事件
     ```

     