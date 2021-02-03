# Spring AOP

## 一、Spring AOP核心概念

在开始使用AOP之前，让我们先来熟悉AOP的概念和术语。 <a style="color:red">这些术语不是Spring特有的，而是与面向方面编程(AOP)有关</a>

| 术语                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 方面/切面(`Aspect`)       | 一个具有一组API的模块，提供交叉要求。例如，日志记录模块被称为AOP方面用于记录。应用程序可以根据需要具有任意数量的方面。 |
| 加入点(`Join point`)      | 这表示你的应用程序中可以插入AOP方面的一点。也可以说，这是应用程序中使用Spring AOP框架采取操作的实际位置。 |
| 通知(`Advice`)            | 这是在方法执行之前或之后采取的实际操作。 这是在Spring AOP框架的程序执行期间调用的实际代码片段。 |
| 切入点(`Pointcut`)        | 这是一组一个或多个连接点，其中应该执行通知(`Advice`)。 您可以使用表达式或模式指定切入点，我们将在AOP示例中看到。 |
| 介绍(`Introduction`)      | 介绍允许向现有类添加新的方法或属性。                         |
| 目标对象(`Target object`) | 对象被一个或多个方面通知(`Advice`)，该对象将始终是代理的对象。也称为通知(`Advice`)对象。 |
| 编织(`Weaving`)           | 编织是将方面与其他应用程序类型或对象进行链接以创建通知(`Advice`)对象的过程。 这可以在编译时，加载时间或运行时完成。 |

## 二、Spring AOP通知类型

Spring AOP中可以使用以下五种建议：

| 通知              | 描述                                                   |
| ----------------- | ------------------------------------------------------ |
| `before`          | 在方法执行之前运行通知。                               |
| `after`           | 在方法执行后运行通知，无论其结果如何。                 |
| `after-returning` | 只有方法成功完成后才能在方法执行后运行通知。           |
| `after-throwing`  | 只有在方法通过抛出异常而退出方法执行之后才能运行通知。 |
| `around`          | 在调用通知方法之前和之后运行通知。                     |

## 三、Spring AOP实现示例

Spring支持使用`@AspectJ`注释样式方法和基于模式的方法来实现自定义方面。

### 1）基于XML模式

方面(`Aspects`)使用常规类以及基于XML的配置来实现。
要使用本节中描述的`aop`命名空间标签，您需要按照以下所述导入`spring-aop`模式：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

   <!-- bean definition & AOP specific configuration -->

</beans>
```

#### 声明一个方面(`Aspects`)

使用`<aop：aspect>`元素声明一个方面(`Aspects`)，并使用`ref`属性引用后台`bean`，如下所示：

```xml
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">
   ...
   </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
...
</bean>
```

这个“`aBean`”将被配置和依赖注入就像任何其他的`Spring Bean`一样，就像在前几章中看到的一样。

#### 声明一个切入点

切入点(`pointcut`)有助于确定要用不同建议执行的关联点(即方法)。 在使用基于XML模式的配置时，切入点将定义如下：

```xml
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">

   <aop:pointcut id="businessService"
      expression="execution(* com.xyz.myapp.service.*.*(..))"/>
   ...
   </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
...
</bean>
```

以下示例定义了一个名为“`businessService`”的切入点，该切入点将匹配`com.yiibai`包中`Student`类中的`getName()`方法的执行：

```xml
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">

   <aop:pointcut id="businessService"
      expression="execution(* com.yiibai.Student.getName(..))"/>
   ...
   </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
...
</bean>
```

#### 声明通知

您可以使用`<aop：{ADVICE NAME}>`元素在`<aop：aspect>`内的五个通知中的任何一个声明如下：

```xml
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">
      <aop:pointcut id="businessService"
         expression="execution(* com.xyz.myapp.service.*.*(..))"/>

      <!-- a before advice definition -->
      <aop:before pointcut-ref="businessService" 
         method="doRequiredTask"/>

      <!-- an after advice definition -->
      <aop:after pointcut-ref="businessService" 
         method="doRequiredTask"/>

      <!-- an after-returning advice definition -->
      <!--The doRequiredTask method must have parameter named retVal -->
      <aop:after-returning pointcut-ref="businessService"
         returning="retVal"
         method="doRequiredTask"/>

      <!-- an after-throwing advice definition -->
      <!--The doRequiredTask method must have parameter named ex -->
      <aop:after-throwing pointcut-ref="businessService"
         throwing="ex"
         method="doRequiredTask"/>

      <!-- an around advice definition -->
      <aop:around pointcut-ref="businessService" 
         method="doRequiredTask"/>
   ...
   </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
...
</bean>
```

可以对不同的通知使用相同`doRequiredTask`或不同的方法。 这些方法将被定义为方面模块的一部分。

### 2）基于@AspectJ

`@AspectJ`是指将Java方法注释为Java 5注释的常规Java类的方式。 `@AspectJ`是指将Java方法注释为Java 5注释的常规Java类的方式。通过在基于XML Schema的配置文件中包含以下元素来启用`@AspectJ`支持。

```xml
<aop:aspectj-autoproxy/>
```

#### 声明一个方面(aspect)

方面(`aspect`)的类就像任何其他正常的bean一样，并且可以像任何其他类一样具有方法和字段，不过它们使用`@Aspect`进行注释，如下所示：

```java
package org.xyz;

import org.aspectj.lang.annotation.Aspect;

@Aspect
public class AspectModule {

}
```

它们就像任何其他以XML格式配置的`bean`一样，如下所示：

```xml
<bean id="myAspect" class="org.xyz.AspectModule">
   <!-- configure properties of aspect here as normal -->
</bean>
```

#### 声明一个切入点

切入点(`pointcut`)有助于确定要用不同通知执行的关联点(即方法)。 在使用基于`@AspectJ`的配置时，切入点声明有两部分：

- 一个切入点表达式，确定哪些方法执行。
- 切入点签名包括名称和任意数量的参数。 该方法的实体是无关紧要的，也可以是空的。

以下示例定义了一个名为“`businessService`”的切入点，该切入点将匹配`com.xyz.myapp.service`包下的类中可用的每个方法的执行：

```java
import org.aspectj.lang.annotation.Pointcut;

@Pointcut("execution(* com.xyz.myapp.service.*.*(..))") // expression 
private void businessService() {}  // signature
```

以下示例定义了一个名为“`getname`”的切入点，该切入点将与`com.yiibai`包下的`Student`类中的`getName()`方法的执行相匹配：

```java
import org.aspectj.lang.annotation.Pointcut;

@Pointcut("execution(* com.yiibai.Student.getName(..))") 
private void getname() {}
```

#### 声明通知

您可以使用`@{ADVICE-NAME}`注释在以下所述的五个建议中声明任何一个。假设您已经定义了一个切入点签名方法为`businessService()`，参考以下配置：

```java
@Before("businessService()")
public void doBeforeTask(){
 ...
}

@After("businessService()")
public void doAfterTask(){
 ...
}

@AfterReturning(pointcut = "businessService()", returning="retVal")
public void doAfterReturnningTask(Object retVal){
  // you can intercept retVal here.
  ...
}

@AfterThrowing(pointcut = "businessService()", throwing="ex")
public void doAfterThrowingTask(Exception ex){
  // you can intercept thrown exception here.
  ...
}

@Around("businessService()")
public void doAroundTask(){
 ...
}
```

可以为任何通知定义切入点内嵌。 下面是一个为之前通知定义的内联切入点的示例：

```java
@Before("execution(* com.xyz.myapp.service.*.*(..))")
public doBeforeTask(){
 ...
}
```

## 四、Spring AOP实战

准备工作：

首先需要创建一个Maven项目工程，然后在pom文件中引入以下jar：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>SpringAOP</artifactId>
    <version>1.0-SNAPSHOT</version>

<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>5.2.8.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.8.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.6</version>
    </dependency>
</dependencies>
</project>
```



### 1）基于XML的实现

项目结构：

![AOP1](img\AOP1.png)

**LoggingAspect.java**

```java
package com.aop.xml;

/**
 * 全局日志切面
 */
public class LoggingAspect {
    public void beforeAdvice(){
        System.out.println("【方法调用前日志】");
    }

    public void afterAdvice(){
        System.out.println("【方法调用后日志】");
    }

    public void afterReturningAdvice(Object retVal){
        System.out.println("【参数返回日志】Returning:  " + retVal.toString() );
    }

    public void AfterThrowingAdvice(IllegalArgumentException ex){
        System.out.println("【异常抛出日志】There has been an exception: " + ex.toString());
    }
}
```

**Student.java**

```java
package com.aop.xml;

public class Student {
    private String name;
    private Integer age;

    public void setAge(Integer age) {
        this.age = age;
    }
    public Integer getAge() {
        System.out.println("Age : " + age );
        return age;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        System.out.println("Name : " + name );
        return name;
    }
    public void printThrowException(){
        System.out.println("Exception raised");
        throw new IllegalArgumentException();
    }
}
```

**MainAPP.java**

```java
package com.aop;

import com.aop.xml.Student;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context =
                new ClassPathXmlApplicationContext("Beans.xml");

        Student student = (Student) context.getBean("student");
        student.getName();
        student.getAge();
        student.printThrowException();
    }
}
```

**Beans.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

    <aop:config>
        <aop:aspect id="log" ref="logging">
            <aop:pointcut id="selectAll"
                          expression="execution(* com.aop.xml.*.*(..))"/>
            <aop:before pointcut-ref="selectAll" method="beforeAdvice"/>
            <aop:after pointcut-ref="selectAll" method="afterAdvice"/>
            <aop:after-returning pointcut-ref="selectAll"
                                 returning="retVal"
                                 method="afterReturningAdvice"/>
            <aop:after-throwing pointcut-ref="selectAll"
                                throwing="ex"
                                method="AfterThrowingAdvice"/>
        </aop:aspect>
    </aop:config>

    <!-- Definition for student bean -->
    <bean id="student" class="com.aop.xml.Student">
        <property name="name"  value="Test" />
        <property name="age"  value="21"/>
    </bean>

    <!-- Definition for logging aspect -->
    <bean id="logging" class="com.aop.xml.LoggingAspect"/>

</beans>

```

运行结果：

![AOP3](img\AOP3.png)

看了实例以后，我们接下来具体了解基于XML代码中的一些重点概念：

#### 1. 基于XML的切入点

切入点(JoinPoint)代表示您的应用程序中可以插入AOP方面的某个点。 您也可以说，这是应用程序中使用**Spring AOP**框架执行操作的实际位置。 请考虑以下示例：

- 包中包含的所有方法类。
- 一个类的特定方法。

<a style="background:yellow">切入点</a>

切入点(`PointCut`)是一组一个或多个连接点，其中它用来执行通知。可以使用表达式或模式指定切入点，我们将在下面AOP示例中看到。 在Spring，切入点有助于使用特定的连接点来应用通知。 请考虑以下示例：

- `expression="execution(* com.aop.xml.*.*(..))"`
- `expression="execution(* com.aop.xml.Student.getName(..))"`

<a style="background:yellow">语法</a>

```xml
   <aop:config>
       <aop:aspect id="log" ref="adviceClass">
          <aop:pointcut id="pointcut-id" expression="execution( expression )"/>    
       </aop:aspect>
   </aop:config>
```

在上面语法中 -

- `adviceClass` - 关联(ref)的类包含通知方法。
- `pointcut-id` - 切入点的`ID`。
- `execution( expression)` - 涵盖应用通知的方法的表达式。

#### 2.基于XML的Before Advice、After Advice

`Before`和`After`是一种通知类型，可以确保在方法执行前运行通知,以下是`Before`的通知语法：

<a style="background:yellow">语法</a>

```xml
   <aop:config>
   <aop:aspect id="log" ref="logging">
      <aop:pointcut id="pointcut-id" expression="execution( expression )"/>    
      <aop:before pointcut-ref="pointcut-id" method="methodName"/>
   </aop:aspect>
   </aop:config>
```

在上面配置中

- `pointcut-id` - 切入点的id。
- `methodName` - 在调用函数之前调用的函数的方法名称。

同理`After`的通知语法也一样

```xml
 <aop:config>
   <aop:aspect id="log" ref="logging">
      <aop:pointcut id="pointcut-id" expression="execution( expression )"/>    
      <aop:after pointcut-ref="pointcut-id" method="methodName"/>
   </aop:aspect>
   </aop:config>
```

#### 3.基于XML的After Returning Advice

<a style="background:yellow">语法</a>

```xml
  <aop:config>
   <aop:aspect id="log" ref="logging">
      <aop:pointcut id="pointcut-id" expression="execution( expression )"/>    
      <aop:after-returning pointcut-ref="pointcut-id" returning="retVal" method="methodName"/>
   </aop:aspect>
   </aop:config>
```

- `returning`的值是返回的参数

#### 4.基于XML的After Throwing Advice

<a style="background:yellow">语法</a>

```sql
<aop:config>
   <aop:aspect id="log" ref="logging">
      <aop:pointcut id="pointcut-id" expression="execution( expression )"/>    
      <aop:after-throwing pointcut-ref="pointcut-id" throwing="ex" method="methodName"/>
   </aop:aspect>
   </aop:config>
SQL
```

在上面配置中，

- `pointcut-id` - 切入点的id
- `ex` - 要被抛出的异常
- `methodName` - 在调用函数之前调用的函数的方法名称

#### 5.基于XML的Around Advice

`Around`是一种通知类型，可以确保**方法执行前后的通知运行**。 以下是`Around`通知的语法：

<a style="background:yellow">语法</a>

```sql
   <aop:config>
   <aop:aspect id="log" ref="logging">
      <aop:pointcut id="pointcut-id" expression="execution( expression )"/>    
      <aop:around pointcut-ref="pointcut-id" method="methodName"/>
   </aop:aspect>
   </aop:config>
SQL
```

在上面配置中，

- `pointcut-id` - 切入点的id
- `methodName` - 在调用函数之前调用的函数的方法名称

### 2）基于注解的实现

项目结构：

![AOP2](img\AOP2.png)

**LoggingAspect.java**

这里相比注解，<a style="color:red">去除了异常抛出、参数返回的切面方法</a>，原因是xml方式和注解方式还是有些区别的，将在第五大节具体阐述

```java
package com.aop.aspectJ;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

/**
 * 全局日志切面
 */
@Component
@Aspect
public class LoggingAspect {
    @Pointcut("execution(* com.aop.aspectJ.*.*(..))")
    private void selectAll(){}

    @Before("selectAll()")
    public void beforeAdvice(){
        System.out.println("【方法调用前日志】");
    }

    @After("selectAll()")
    public void afterAdvice(){
        System.out.println("【方法调用后日志】");
    }
}
```

**Student.java**与上面一样

**MainAPP.java**

```java
package com.aop;

import com.aop.aspectJ.Student;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context =
                new ClassPathXmlApplicationContext("Beans-aspectj.xml");

        Student student = (Student) context.getBean("student_aspectj");
        student.getName();
        student.getAge();
        student.printThrowException();
    }
}
```

**Beans-aspectj.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

    <aop:aspectj-autoproxy/>

    <!-- Definition for student bean -->
    <bean id="student_aspectj" class="com.aop.aspectJ.Student">
        <property name="name"  value="Test" />
        <property name="age"  value="25"/>
    </bean>

    <!-- Definition for logging aspect -->
    <bean id="logging" class="com.aop.aspectJ.LoggingAspect"/>

</beans>
```

运行结果：

![AOP4](img\AOP4.png)

看了实例以后，我们接下来具体了解基于注解代码中的一些重点概念：

#### 1.基于注解的切入点

连接点(`JoinPoint`)代表您的应用程序中可以插入AOP方面的一个点/位置。 您也可以说，这是应用程序中使用Spring AOP框架执行操作的实际位置。 请考虑以下示例：

- 包中包含的所有方法类。
- 一个类的特定方法。

切入点(`PointCut`)是一组一个或多个连接点，在其中应该执行的通知。 您可以使用表达式或模式指定切入点，我们将在AOP示例中看到。 在Spring中切入点有助于使用特定的连接点来应用通知。 请考虑以下示例：

- `@Pointcut("execution(* com.aop.aspectJ.*.*(..))")`
- `@Pointcut("execution(* com.aop.aspectJ.Student.getName(..))")`

<a style="background:yellow">语法</a>

```java
@Aspect
public class Logging {

   @Pointcut("execution(* com.aop.aspectJ.*.*(..))")
   private void selectAll(){}

}
```

在上面的语法示例中 

- `@Aspect` - 将类标记为包含通知方法的类
- `@Pointcut` - 将函数标记为切入点
- `execution( expression )` - 涵盖应用通知的方法的表达式

#### 2.基于注解的Before Advice、After Advice

`@Before`是一种通知类型，可以确保在方法执行之前运行通知。 以下是`@Before`通知(`advice`)的语法：

<a style="background:yellow">语法</a>

```java
@Pointcut("execution(* com.aop.aspectJ.*.*(..))")
private void selectAll(){}

@Before("selectAll()")
public void beforeAdvice(){
   System.out.println("【方法调用前日志】");
}
```

在上面的语法示例中 -

- `@Pointcut` - 将函数标记为切入点
- `@Before` - 将函数标记为在切入点覆盖的方法之前执行的通知。
- `execution( expression )` - 涵盖应用通知的方法的表达式。

同理`After`的通知语法也一样

```java
@After("selectAll()")
    public void afterAdvice(){
      System.out.println("【方法调用后日志】");
}
```

#### 3.基于注解的After Returning Advice

`@AfterReturning`是一种通知类型，可确保方法执行成功后运行通知。 以下是`@AfterReturning`通知的语法：

<a style="background:yellow">语法</a>

```java
@AfterReturning(value = "selectAll()",returning="retVal")
public void afterReturningAdvice(JoinPoint jp,Object retVal){
    System.out.println("afterReturningAdvice【方法名】："+jp.getSignature());
    System.out.println("afterReturningAdvice【参数返回日志】Returning:  " + retVal.toString() );
}
```

在上面的语法示例中 

- `@AfterReturning` - 如果方法返回成功，则将函数标记为在切入点覆盖的方法之前执行的通知。
- `@Pointcut` - 将函数标记为切入点
- `@After` - 将函数标记为在切入点覆盖的方法之后执行的通知。
- `execution( expression )` - 涵盖应用通知的方法的表达式。
- `returning` - 要返回的变量的名称。

运行结果：可以看到，这个afterReturning是在方法调用之后会得到一些信息，包括参数名，参数等等信息

![AOP5](img\AOP5.png)

#### 4.基于注解的AfterThrowing

`@AfterThrowing`是一种通知类型，可以确保在方法抛出异常时运行一个通知。 以下是`@AfterThrowing`通知的语法：

**语法**

```java
@AfterThrowing(pointcut="execution(* com.aop.aspectJ.Student.*(..))", throwing= "error")
public void afterThrowingAdvice(JoinPoint jp, Throwable error){
   System.out.println("afterThrowingAdvice【方法名】："  + jp.getSignature());  
   System.out.println("afterThrowingAdvice Exception: "+error);  
}

//另外一种写法：
@AfterThrowing(value="selectAll()", throwing= "error")
public void afterThrowingAdvice(JoinPoint jp, Throwable error){
   System.out.println("afterThrowingAdvice【方法名】："  + jp.getSignature());  
   System.out.println("afterThrowingAdvice Exception: "+error);  
}
```

在上面的语法示例中 -

- `@AfterReturning` - 如果方法返回成功，则将函数标记为在切入点覆盖的方法之前执行的通知
- `@Pointcut` - 将函数标记为切入点
- `@After` - 将函数标记为在切入点覆盖的方法之后执行的通知
- `execution( expression )` - 涵盖应用通知的方法的表达式
- `throwing` - 返回的异常名称
- 如果和前面的涵盖应用通知的方法一样，就可以直接用value ，它的值表示某切入点的方法名

运行结果：可以看到它可以获取到抛异常的方法名，以及异常信息

![AOP6](img\AOP6.png)

#### 5.基于注解的Around通知

`@Around`是一种建议类型，可确保方法执行前后的通知可以运行。 以下是`@Around`通知的语法：

**语法**

```java
@Around(value = "selectAll()")
public void aroundServiceMethodExecution( ProceedingJoinPoint pjp) throws Throwable{
    System.out.println("1-------------");
    Object result=pjp.proceed();
    System.out.println("result  "+result);
    System.out.println("2-------------");
}
```

在上面的语法示例中 -

- `@Pointcut` - 将函数标记为切入点
- `execution( expression )` - 涵盖应用通知的方法的表达式。
- `@Around` - 将函数标记为在切入点覆盖的方法之前执行的通知。

运行结果：可以看到Around是环绕在一个方法调用的前后，以`Object result=pjp.proceed();`的调用作为分界

![AOP7](img\AOP7.png)

## 五、Spring AOP高级部分

### 1）Spring AOP代理

到目前为止，我们学习过如何使用`<aop:config>`或`<aop:aspectj-autoproxy>`声明切面

也可以通过编程方式创建代理，并使用代理对象以编程方式调用切面

**语法**

```java
Student student = new Student();
AspectJProxyFactory proxyFactory = new AspectJProxyFactory(student);
proxyFactory.addAspect(LoggingAspect.class);
Student proxyStudent = proxyFactory.getProxy();
//Invoke the proxied method.
proxyStudent.getAge();
```

在上面的语法示例中 

- `AspectJProxyFactory` - 用于创建代理对象的工厂类
- `Logging.class` - 包含通知的方面类
- `Student` - 将被通知的业务类

代码：

与基于注解形式的代码主要区别在启动类，和xml文件

**MainApp.java**

```java
package com.aop;

import com.aop.aspectJ.LoggingAspect;
import com.aop.aspectJ.Student;
import org.springframework.aop.aspectj.annotation.AspectJProxyFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MainApp {
    public static void main(String[] args) {
       ApplicationContext context =
                new ClassPathXmlApplicationContext("Beans-aspectj.xml");

        Student student = (Student) context.getBean("student_aspectj");
        //创建一个代理工厂
        AspectJProxyFactory proxyFactory = new AspectJProxyFactory(student);
        //加入一个切面类到这个工厂
        proxyFactory.addAspect(LoggingAspect.class);
        //新建一个学生类的代理对象
        Student proxyStudent = proxyFactory.getProxy();
        //调用这个代理对象的方法
        proxyStudent.getAge();
    }
}
```

**Beans-aspectj.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

<!--    <aop:aspectj-autoproxy/>-->

    <!-- Definition for student bean -->
    <bean id="student_aspectj" class="com.aop.aspectJ.Student">
        <property name="name"  value="Test" />
        <property name="age"  value="25"/>
    </bean>

    <!-- Definition for logging aspect -->
    <bean id="logging" class="com.aop.aspectJ.LoggingAspect"/>

</beans>
```

运行结果：

![AOP8](img\AOP8.png)

### 2）Spring AOP自定义注解

根据切入点表达式可能会将它们应用到其他不需要通知的其他`bean`。 例如，考虑以下表达式：

```java
execution(* com.aop.aspectJ.*.getAge(..))
```

如果一个新的spring bean添加了`getAge()`方法，并且通知将开始应用到它，尽管它可能不需要应用通知

要实现不需要它，我们可以创建一个自定义注解并注解要应用到通知的方法

```java
@Before("@annotation(com.aop.aspectJ.Loggable)")
```

实例：

代码结构：

![AOP9](img\AOP9.png)

**Loggable.java**

```java
package com.aop.aspectJ;

/**
 * 自定义注解
 */
public @interface Loggable {
}
```

**Student.java**

我们在需要通知的方法上添加了我们自定义的注解`@Loggable`

```java
package com.aop.aspectJ;

public class Student {
    private String name;
    private Integer age;

    public void setAge(Integer age) {
        this.age = age;
    }

    @Loggable
    public Integer getAge() {
        System.out.println("Age : " + age );
        return age;
    }
    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        System.out.println("Name : " + name );
        return name;
    }
    public void printThrowException(){
        System.out.println("Exception raised");
        throw new IllegalArgumentException();
    }
}
```

其他部分同Spring AOP代理的代码即可

运行结果：

![AOP10](img\AOP10.png)