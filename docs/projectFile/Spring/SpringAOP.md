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
XML
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

### 2）基于[@AspectJ](https://github.com/AspectJ)

`@AspectJ`是指将Java方法注释为Java 5注释的常规Java类的方式。 `@AspectJ`是指将Java方法注释为Java 5注释的常规Java类的方式。通过在基于XML Schema的配置文件中包含以下元素来启用`@AspectJ`支持。

```xml
<aop:aspectj-autoproxy/>
XML
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

### 2）基于注解的实现

项目结构：

![AOP2](img\AOP2.png)

**LoggingAspect.java**

这里相比注解，去除了异常抛出、参数返回的切面方法，原因是xml方式和注解方式还是有些区别的，将在第五大节具体阐述

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