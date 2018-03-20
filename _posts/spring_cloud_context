Spring Cloud 上下文

 标签： Spring Cloud

---

#### **1、引导应用程序上下文**
        在应用程序上下文之上，还存在一个上下文，这就是引导(bootstrap)上下文。  
        bootstrap上下文是应用程序的父上下文。bootstrap 上下文开箱即用，负责从应用程序之外加载配置属性、加解密外部文件中的属性。bootstrap的优先级高，在默认情况下是不能被本地配置覆盖的。
        可以通过如下配置来禁用引导过程：
        `spring.cloud.bootstrap.enabled=false`

#### **2、应用程序上下文层次结构**
        如果是从 SpringApplication或者 SpringApplicationBuilder 构建引用程序，那么 bootstrap上下文就会自动添加为该上下文的父级。这个是Spring的一个特点，子上下文会继承父上下文的属性源和配置文件。因此与不使用 Spring Cloud Config 构建上下文相比，main 应用程序上下文将会包含额外的属性源。  
        这些额外的属性源包括：
        * bootstrap: 如果在 bootstrap 上下文中找到任何的 PropertySourceLocators,则可选CompositePropertySource 作为最高优先级，并且具有非空属性。  
            一个例子就是 Spring Cloud Confgi Server 的 properties。
        * applicationConfig：[classpath：bootstrap.yml]（如果Spring 的 profile 是活动的，则bootstrap.yml 和 Spring的配置文件是同级的）,并且这些属性会被用于配置引导上下文，  
        然后在设置父上下文时将这些属性加入到子上下文中。它们的优先级低于application.yml，并且作为创建Spring Boot应用程序过长的正常部分添加到子级的任何其他属性源。

       由于属性源的排序规则，引导属性源的条目优先，但是这个优先并不包括来自bootstrap.yml 的任何数据，bootstrap.yml 数据具有非常低的优先级，但是这个数据可以用来设置默认值。

       你可以简单的设置你创建的 ApplicationContext 的父上下文来扩展上下文层次结构。  
           例如：使用自己定义的接口；SpringApplicationBuilder 提供的便利方法parent()、child()或者sibling().
       此时，引导上下文将会是你自己创建的上下文中最高级的上下文的父级。在这个上下文层次机构中，每一个上下文都有自己的引导属性源（可能为空）,以避免无意中将父上下文的属性传递到子上下文中。如果存在配置服务，则每一个上下文也可以拥有不同的 spring.application.name，不同的远程属性源。常规的Spring 应用程序上下文的行为规则适用于属性解析，如果属性名和属性源相同，则子上下文的属性会覆盖父上下文的属性。

       请注意，SpringApplicationBuilder允许您在整个层次结构中共享Environment，但这不是默认值。因此，统计山下问尤其不需要具有相同的配置文件或者属性源，尽管它们与父母共享共同点。
#### **3、改变引导位置properties**
       可以使用 spring.cloud.bootstrap.name(默认为bootstrap)或者spring.cloud.bootstrap.location(默认为empty) 指定 bootstrap.yml(或properties)的位置。 例如，在系统属性中，像这些拥有相同名称（类似于spring.config.\*）的属性，实际上通过在Environment中设置这些属性来引导 ApplicationContext,如果正在构建的上下文中有激活的 profile(来自 Spring.profiles.active或通过Environment API),那么该配置文件中的属性也将被加载，就像常规的 Spring Boot 应用程序。


#### **4、覆盖远程Properties属性值**
       通过引导上下文添加到应用程序的属性源通过是“远程的”（例如是配置服务器），并且在默认情况下，是不能被本地覆盖的。如果你希望应用程序使用自己的系统属性或配置文件覆盖远程的属性，则远程属性源必须通过设置 spring.cloud.config.allowOverride=true(在本地设置无效) 来授予权限。一旦设置了该标志，就会有一些更精细的设置来控制远程属性与系统属性和应用程序本地配置的位置：spring.cloud.config.overrideNone=true覆盖任何本地属性源，如果只有系统属性和环境变量（而不是本地配置文件）应该覆盖远程设置时可以使用spring.cloud.config.overrideSystemProperties=false。
#### **5、自定义引导配置**
       通过添加条目到org.springframework.cloud.bootstrap.BootstrapConfiguration 键下的/META-INF/spring.factories 来指导引导上下文做任何事情。这是一个使用逗号分隔的添加了Spring @Configuration 注解的类（用于创建上下文）的列表。你可以在此处创建要用于自动装配的主应用程序上下文的任何bean，并且还有关于ApplicationContextInitializer 类型的@Bean 的特殊规约。类可以使用  @Order 标记来控制启动顺序（默认最后启动）。
       注意：在自定义 BootstrapConfiguration时，你添加的类不应该被错误添加到主应用程序中，它们可能不需要被使用。对于那些不在被 @ComponentScan 或者 @SpringBootApplication 注解标注的类可以使用单独的包。

       引导过程随着将初始化器注入到主SpringApplication实例中（即正常的Spring Boot启动顺序，无论是作为独立的应用程序还是部署咋应用程序服务器中）结束。首先，从 spring。factories 中找到的类创建引导上下文；然后，在主应用程序启动之前，所有的被标注@Beans的ApplicationContextInitializer类型的Bean将会被加入的主 SpringApplication中。

#### **6、自定义引导属性源**
       引导过程中添加的外部属性源默认是 Config Server，但是你可以通过将 PropertySourceLocator 类型的Bean添加到引导上下文中来添加额外的属性；你也可以使用一下方法从不同的服务或者数据库中插入额外的属性。
       例如：自定义定位器
       ```java
          @Configuration
          public class CustomPropertySourceLocator imolements PropertySourceLocator{
            @Override
            public PropertySource<?> locate(Environment environment){
              return new MapPropertySource("customProperty",Collections.<String.Objtec>singletonMap("property.from.sample.custom.source","worked as intended"));
            }
          }
       ```  
       传入的Environment是要创建ApplicationContext 的Environment，即我们提供额外属性来源的Environment,它将已经具有正常的Spring Boot提供的属性源，因此，你能够使用它们来定位特定于Environment的属性源位置（例如通过将其绑定在spring.application.name上，如在默认情况下所做的那样Config Server属性源定位器）。
       如果你把这个类中创建在一个ja中r，然后添加一个META-INF/spring.factories包含：org.springframework.cloud.bootstrap.BootstrapConfiguration=sample.custom.CustomPropertySourceLocator，那么“customProperty” PropertySource将显示在其类路径中包含该jar的任何应用程序中。
#### **7、环境变化**
       应用程序会监听 EnvironmentChangeEvent 事件，并且以几种标准的方式作出改变，用户可以常规的方式添加 @Bean 的方式添加ApplicationListeners 。当EnvironmentChangeEvent 事件被观察到时，应用程序将会拥有一个已更改的键值列表，此时应用程序将会使用这些键值列表做以下操作：  
          1. 重新绑定上下文中使用 @ConfigurationProperties 注解修饰的bean.
          2. 为logging.level.\*中的任何属性设置 logger 级别  
       注意：配置客户端不会通过轮询主动查询 Environment的改变。通常我们不建议实现检测变更的方法（即使你能够使用 @Scheduled 注解进行设置）。如果你有一个扩展的客户端应用程序，那么最好把 EnvironmentChangeEvent 广播到所有实例，而不是主动轮询这些变更（例如：使用 Spring Cluod Bus）。
       EnvironmentChangeEvent 涵盖了大量的刷新用例，只要你能够更改 Environment 并且能够发布这些事件（这些 API 是Spring Core 公开的），你可以通过访问 /configprops 端点（常规Spring Boot 执行器功能）来确定这个变更过是否绑定到 @ConfigurationProperties注解修饰的类。
       例如：DataSource 可以在运行时更改它的 maxPoolSize 属性（由 Spring Boot 默认创建的 DataSOurce 一个 @ConfigurationProperties bean）来动态改变容量。在你需要更多的控制刷新或者需要原子改变整个 ApplicationContext 的地方重新绑定@ConfigurationProperties是不会覆盖其他大量用例的。为了解决这些担忧，我们提供了 @RefreshScope
#### **8、刷新范围**
       当配置更改时，被标记了@RefreshScope的Spring @Bean 将会被特殊处理。这解决了有状态的Bean在初始化只注入配置的问题。例如：如果通过Environment更改数据库URL时DataSource有开放连接，我们或许希望这些链接的持有者（一般是Thread）能够它们正在做的工作。然后下一次再有其他用户（线程）从连接池中拿走一个连接时，这个连接将使用新的URL。  
       这个 Refresh Scope Bean 是在使用时（方法被调用）的懒代理，并且作用域作为初始值的缓存。若要在下一个方法调用时强制重新初始化一个Bean，只需要使其缓存的条目失效。
       RefreshScope 是上下文中的一个 bean ，它有一个 public 的方法 refreshAll() 通过清除目标的缓存来刷新范围内所有的bean。同样，它还有一个 public 的 refresh(String) 方法 通过方法的名字来刷新单个的bean。这个功能在 /refresh 端点中公开（通过HTTP或者 JMX）。
       @RefreshScope在标记了@Configuration的类上工作，但是可能产生一些意料之外的行为。例如：如果一个类标记了
       @RefreshScope，但并不意味着这个类中所有定义的 @Bean 都是 @RefreshScope。具体来说，任何依赖这些bean（@RefreshScope）

任何取决于这些bean的东西都不能依赖它们在刷新启动时被更新
#### **9、加密和解密**
#### **10、端点**
