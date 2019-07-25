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
> 2. 使用注解装配
> 3. 自动装配
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
3. 命名空间装配
### 10.2.2 使用注解装配
> 1. @Compontent
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
####
### note
1. 装配方式的选用：自动装配 > 使用类和接口装配 > 使用XML文件装配（约定优于配置 > 减少XML文件的使用 > XML）

