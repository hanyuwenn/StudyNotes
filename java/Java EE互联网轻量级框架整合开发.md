# ch 10 装配spring bean
## 目标
> 1. 三种依赖注入
> 2. 使用xml注入bean
> 3. 使用注解注入bean, 及消除歧义的方式
> 4. 使用profile和条件装配bean
> 5. Bean的作用域
> 6. 了解Spring EL的使用

## 10.1 依赖注入的三种方式
> 1. 构造器注入
> 2. setter方法注入
> 3. 接口注入
### 10.1.1 构造器注入
Spring 根据提供的构造器参数信息和类信息通过反射来创建对象
```
<bean id=” rolel ” class=” com.ssm.chapter9.pojo.Role” >
  <constructor-arg index = "0" value ＝ "总经理" />
  <constructor-arg index = "1" value ＝ "公司管理者" />
</bean >
```
### 10.1.2 setter方法注入
Spring 根据提供的类信息，属性信息通过反射调用类的无参构造器创建对象，并通过Setter方法将值注入到创建的对象中。 *需要提供无参构造器*
```
<bean id=” role2 ” class=” com . ssm . chapter9.pojo.Role” >
  <property name ＝ "roleName" value ＝ "高级工程师" />
  <property name ＝ "note" value ＝ "重要人员" />
</bean>
```
### 10.1.3 接口注入

## 10.2 装配Bean
> 1. 使用XML装配
> 2. 自动装配
> 3. 使用注解装配
### 10.2.1 使用XML装配
> 1. 装配基本类型
> 2. 装配集合
> 3. 命名空间装配

> XSD文件的导入
> 
```
<?xml version ＝ '1. 0' encoding='UTF-8?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation= "http://www.springframework.org/schema/beans
  http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
  〈！一Spring Bean 配置代码一〉
</beans>
```
1. 装配简易值。通过指定Class, id, 需要注入的属性来装配
```
<bean id="source" class ="com.ssm.chapter9.pojo.Source">
    <property name = "fruit" value = "橙汁"/>
    <property name = "sugar" value = "少糖"/>
    <property name ="size" value = "大杯"/>
</bean>
<bean id="juiceMaker2"class ="com.ssm.chapter9.pojo.JuiceMaker2">
    <property name ="beverageShop" value ="贡茶"/>
    <property nane ="source" ref="source"/>
</bean>
```
2. 装配集合
```
<bean id="userRoleAssembly" class="com.ssm.chapter10.pojo.UserRoleAssembly">
  <property name="id" value="1" />
  <property name="list">
    <list>
      <ref bean="role1" />
      <ref bean="role2" />
    </list>
  </property>
  <property name="map">
    <map>
      <entry key-ref="role1" value-ref="user1" />
      <entry key-ref="role2" value-ref="user2" />
    </map>
  </property>
  <property name="set">
    <set>
      <ref bean="role1" />
      <ref bean="role2" />
    </set>
  </property>
</bean>
```
### 10.2.2 命名空间装配
### 10.2.3 使用注解装配
1. @Compontent:使用注解标注bean
```
@Component(value = "role")
public class Role {
}
```
2. @Value: 向对象的属性中注入值
```
@Component(value = "role")
public class Role {
	@Value("1")
	private Long id;
	@Value("role_name_1")
	private String roleName;
	@Value("role_note_1")
	private String note;
}
```
3. @ComponentScan：配置扫描路径或者扫描的类，默认为该类所在包的路径
```
@ComponentScan    // 配置扫描路径，默认为该类所在包的路径
public class PojoConfig {

}
```
```
@ComponentScan(basePackageClasses = { Role.class, RoleServiceImpl.class })  // 可以配置需要扫描的类或者使用包名配置扫描路径
// @ComponentScan(basePackages = {"com.ssm.chapter10.annotation.pojo",
// "com.ssm.chapter10.annotation.service"})
// @ComponentScan(basePackages = {"com.ssm.chapter10.annotation.pojo",
// "com.ssm.chapter10.annotation.service"},
// basePackageClasses = {Role.class, RoleServiceImpl.class})
public class ApplicationConfig {
	
}
```
4. @Autowired:自动装配(当类的属性有多个实现类时会有歧义性)
```
// @Autowired 注解到属性或者setter方法上
@Component("RoleService2")
public class RoleServiceImpl2 implements RoleService2 {

	@Autowired
	private Role role = null;

	public Role getRole() {
		return role;
	}

//	@Autowired
	public void setRole(Role role) {
		this.role = role;
	}
}
```
```
// @Autowired 注解到构造器中
@Component
public class RoleController2 {
	
	private RoleService roleService = null;
	
	public RoleController2(@Autowired @Qualifier("roleService3") RoleService roleService) {
	    this.roleService = roleService;
	}
	
}
```
5. @Primary: 当接口有多个实现类时，优先注入该类
```
@Component("roleService3")
@Primary
public class RoleServiceImpl3 implements RoleService {
}
```
6. @Qualifier: 按名称查找类
```
@Component
public class RoleController {
	@Autowired
	@Qualifier("roleService3")
	private RoleService roleService = null;
}
```
7. @Bean：将方法的返回值作为bean交给spring管理
```
@Component
public class DataSourceBean {

	@Bean(name = "dataSource1")
	public DataSource getDataSource() {
		return null;
	}
}
```
```
// 通过@Bean指定bean的init和destory方法
@Bean(name="juiceMaker2", initMethod="init", destroyMethod="myDestroy")
public JuiceMaker2 initJuiceMaker2() {
	JuiceMaker2 juiceMaker2 = new JuiceMaker2();
	juiceMaker2.setBeverageShop("贡茶");
	Source source = new Source();
	source.setFruit("橙子");
	source.setSize("大杯");
	source.setSugar("少糖");
     juiceMaker2.setSource(source);
	return juiceMaker2;
}
```
8. @ImportResource: 导入XML配置文件
```
@ComponentScan(basePackageClasses = { Role.class, RoleServiceImpl.class })
@ImportResource({"classpath:spring-dataSource.xml"})
public class ApplicationConfig {
	
}
```
9. @Import: 导入其他配置类
```
@ComponentScan(basePackageClasses = { Role.class, RoleServiceImpl.class })
@Import({Applicationconfig2.class, ApplicationConfig3.class})
public class ApplicationConfig {
	
}
```
## 10.3 Profile
> 1. 配置Profile
> 2. 启动Profile
### 10.3.1 配置Profile
> 1. 使用注解配置
> 2. 使用xml配置
1. 在使用Spring MVC时可以通过配置web上下文参数或者DispatchServlet参数
2. 作为JNDI条目
3. 配置JVM启动参数
4. 再集成测试环境中可以使用@ActiveProfiles

## 10.4 加载配置文件
> 1. 使用注解@PropertySource
> 2. 使用xml
1. 使用注解@PropertySource
```
@Configuration
@PropertySource(value={"classpath:database-config.properties"}, ignoreResourceNotFound=true)
public class ApplicationConfig {

}
```
2. 使用xml
```
<?xml version='1.0' encoding='UTF-8' ?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context-4.0.xsd">
    
	<context:component-scan base-package="com.ssm.chapter10.annotation" />
	<!-- 
	<context:property-placeholder
		ignore-resource-not-found="false" location="classpath:database-config.properties" />
      -->
     <!--字符串数组，可配置多个属性文件 --> 
	<bean
		class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		
		<property name="locations">
			<array>
				<value>classpath:database-config.properties</value>
				<value>classpath:log4j.properties</value>
			</array>
		</property>
		<property name="ignoreResourceNotFound" value="false" />
	</bean>
</beans>
```
## 10.5 条件化装配Bean
1. Spring 提供了注解@Conditional 去配置，通过它可以配置一个或者多个类，只是这些类都需要实现接口Condition
```
	@Bean(name = "dataSource")
	@Conditional({DataSourceCondition.class})
	public DataSource getDataSource(
			@Value("${jdbc.database.driver}") String driver,
			@Value("${jdbc.database.url}") String url,
			@Value("${jdbc.database.username}") String username, 
			@Value("${jdbc.database.password}") String password) {
		Properties props = new Properties();
		props.setProperty("driver", driver);
		props.setProperty("url", url);
		props.setProperty("username", username);
		props.setProperty("password", password);
		DataSource dataSource = null;
		try {
			dataSource = BasicDataSourceFactory.createDataSource(props);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return dataSource;
	}
```
```
public class DataSourceCondition implements Condition {
	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		//获取上下文环境
		Environment env = context.getEnvironment();
		//判断是否存在关于数据源的基础配置
		return env.containsProperty("jdbc.database.driver") 
				&& env.containsProperty("jdbc.database.url")
				&& env.containsProperty("jdbc.database.username")
				&& env.containsProperty("jdbc.database.password");
	}
}
```
## 10.6 Bean的作用域
1. 单例（singleton）:#默认#的选项，在spring容器中只生成一个bean
2. 原型（prototype）：当每次注入或者从spring容器中获取bean时创建一个新的实例
3. 会话（session）:在一个session中只创建一个实例
4. 请求（request）：在一次请求中Spring会创建一个实例，不同的请求会创建不同的实例
## 10.7 EL
# 11 面向切面编程
> 1. Spring AOP的基本概念
> 2. 
## 11.1 Sping AOP的基本概念
1. AOP:在某一段流程的前后插入要执行的代码
2. 术语：
- 切面(Aspect)：插入的要执行的代码
- 通知(Adice):
	- before
	- after
	- afterReturning
	- afterThrowing
	- arroundThrowing
- 引入(Introduction)
- 切点(Pointcut):被切面拦截的方法
- 连接点(join point):指定切点
- 织入(Weaving):生成动态代理的过程
3. Spring中的AOP的实现方式（只支持方法拦截）
- 使用ProxyFactoryBean和对应的接口实现
- 使用xml配置
- 使用@AspectJ注解驱动切面
- 使用AspectJ注入切面
## 11.3 @AspectJ
1. 选择切点。将RoleServiceImpl类中的printRole作为切入点
```
public interface RoleService {
	public void printRole(Role role);	
}

```
```
@Component
public class RoleServiceImpl implements RoleService {
	@Override
	public void printRole(Role role) {
		System.out.println("{id: " + role.getId() + ", " 
	        + "role_name : " + role.getRoleName() + ", "
	        + "note : " + role.getNote() + "}");
	}
}
```
2. 创建切面
```
@Aspect
public class RoleAspect {

}
```
3. 连接点
```
@Aspect
public class RoleAspect {
	
	@Pointcut("execution(* com.ssm.chapter11.aop.service.impl.RoleServiceImpl.printRole(..))")
	public void print() {
	}

}
```
4. 通知
```
@Aspect
public class RoleAspect {
    
	...

	@Before("print()")
	public void before() {
		System.out.println("before ....");
	}

	@After("print()")
	public void after() {
		System.out.println("after ....");
	}

	@AfterReturning("print()")
	public void afterReturning() {
		System.out.println("afterReturning ....");
	}

	@AfterThrowing("print()")
	public void afterThrowing() {
		System.out.println("afterThrowing ....");
	}
	
	...
	
}
```
5. 使用方法
```
// 配置类
@Configuration
@EnableAspectJAutoProxy
@ComponentScan(basePackages = "com.ssm.chapter11.aop")
public class AopConfig {
	
	@Bean
    public RoleAspect getRoleAspect() {
        return new RoleAspect();
    }
}
```
```
//运行
	private static void testAnnotation() {
		ApplicationContext context = new AnnotationConfigApplicationContext(AopConfig.class);
		Role role = new Role();
		role.setId(1L);
		role.setRoleName("role_name_1");
		role.setNote("note_1");
		RoleService roleService = context.getBean(RoleService.class);
		roleService.printRole(role);
	}
```
6. 环绕通知
```
@Aspect
public class RoleAspect {
    
	...

	@Around("print()")
	public void around(ProceedingJoinPoint jp) {
		System.out.println("around before ....");
		try {
			jp.proceed();
		} catch (Throwable e) {
			e.printStackTrace();
		}
		System.out.println("around after ....");
	}
	
	...
	
}
```
7. 给通知传入参数
```
@Aspect
public class RoleAspect {
    
	...

	@Before("execution(* com.ssm.chapter11.aop.service.impl.RoleServiceImpl.printRole(..)) " + "&& args(role, sort)")
	public void before(Role role, int sort) {
		System.out.println("before ....");
	}
	
	...
	
}
```



####
### note
1. 装配方式的选用：自动装配 > 使用类和接口装配 > 使用XML文件装配（约定优于配置 > 减少XML文件的使用 > XML）

