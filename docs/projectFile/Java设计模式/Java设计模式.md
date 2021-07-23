## 前言

正如小傅哥所言，我现在写代码的状态基本就是这样：定义属性、创建方法、调用展示

**这也使我不断产生疑问，我写的代码究竟如何？仅仅满足业务的需求，按照PRD完成任务就可以了吗？**

![image-20210722160149299](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210722160149299.png)

其实之前也看过设计模式，但是并没有深入思考，很多都没有加入实战

因此想通过Guide哥提出的读书活动中，好好重学设计模式！

## 设计模式的六大原则

**1、开闭原则（Open Close Principle）**

开闭原则的意思是：**对扩展开放，对修改关闭**。在程序需要进行拓展的时候，不能去修改原有的代码，实现一个热插拔的效果。简言之，是为了使程序的扩展性好，易于维护和升级。想要达到这样的效果，我们需要使用接口和抽象类。

**2、里氏代换原则（Liskov Substitution Principle）**

里氏代换原则是面向对象设计的基本原则之一。 里氏代换原则中说，任何基类可以出现的地方，子类一定可以出现。LSP 是继承复用的基石，只有当派生类可以替换掉基类，且软件单位的功能不受到影响时，基类才能真正被复用，而派生类也能够在基类的基础上增加新的行为。里氏代换原则是对开闭原则的补充。实现开闭原则的关键步骤就是抽象化，而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。

**3、依赖倒转原则（Dependence Inversion Principle）**

这个原则是开闭原则的基础，具体内容：针对接口编程，依赖于抽象而不依赖于具体。

**4、接口隔离原则（Interface Segregation Principle）**

这个原则的意思是：使用多个隔离的接口，比使用单个接口要好。它还有另外一个意思是：降低类之间的耦合度。由此可见，其实设计模式就是从大型软件架构出发、便于升级和维护的软件设计思想，它强调降低依赖，降低耦合。

**5、迪米特法则，又称最少知道原则（Demeter Principle）**

最少知道原则是指：一个实体应当尽量少地与其他实体之间发生相互作用，使得系统功能模块相对独立。

**6、合成复用原则（Composite Reuse Principle）**

合成复用原则是指：尽量使用合成/聚合的方式，而不是使用继承。

## 创建者模式<font color=green>（5节）</font>

这类模式提供创建对象的机制， 能够提升已有代码的灵活性和可复⽤用性。

### 一、工厂模式（<font color=red>Factory Pattern</font>）

工厂模式（Factory Pattern）它提供了一种创建对象的最佳方式，在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

小傅哥的例子很好，特别是反例，很经典的一个场景，但是这个例子对于初学者而言，理解起来还是感觉不是很直观。

我觉得一个很典型也比较好理解的例子就是[菜鸟教程](https://www.runoob.com/design-pattern/factory-pattern.html)中的示例，如下图有一个专门绘画形状的（接口），实现这个接口就可以按照需求绘画不同的形状，例如有一个类circle继承了这个Shape 然后在这个方法中填充生产圆形的方法...

![工厂模式的 UML 图](https://gitee.com/JongcyChen/PicBed/raw/master/img/AB6B814A-0B09-4863-93D6-1E22D6B07FF8.jpg)

我是在这个代码的基础上增加了一个**形状枚举**

![image-20210723170446248](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210723170446248.png)

```java
package demo.enums;

import lombok.AllArgsConstructor;
import lombok.Getter;

@Getter
@AllArgsConstructor
public enum ShapeEnum {
    CIRCLE(1,"CIRCLE"),
    RECTANGLE(2,"RECTANGLE"),
    SQUARE(3,"SQUARE");


    Integer key;
    String value;

    public static ShapeEnum getShapeEnumByKey(Integer key){
        if (key == null) {
            return null;
        }
        for (ShapeEnum item : ShapeEnum.values()) {
            if (item.key.equals(key)) {
                return item;
            }
        }
        return null;
    }

    public static String getValueByKey(Integer key){
        ShapeEnum shapeEnum = getShapeEnumByKey(key);
        if(null != shapeEnum){
            return shapeEnum.getValue();
        }
        return null;
    }

}
```

运行结果：

![image-20210723170557160](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210723170557160.png)

从这个示例就可以看出，它存在很多优点：

1. 满足了单一职责原则，一个类一个方法只干一件事，每一个业务逻辑都在自己所属的类中完成，并且将创建者都交给了工厂来做，避免了创建对象和方法逻辑的耦合；
2. 满足开闭原则，对修改关闭对扩展开放，如果你现在想画一个椭圆，只需要实现这个`IShape`接口的`draw`方法即可。

同时也会有一些问题所在，例如需要画几十种图形，那么要就要创建上几十个类，这样显然也会造成臃肿，并且工厂得一直if else传递下去也不现实~