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
> 2. 使用类和接口装配
> 3. 自动装配
### 10.2.1 
### note
1. 装配方式的选用

