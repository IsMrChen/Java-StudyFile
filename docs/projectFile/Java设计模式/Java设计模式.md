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

### 二、抽象工厂模式（<font color=red>Abstract Factory Pattern</font>）

看到抽象工厂模式，我会想到抽象工厂模式是不是用到了abstract（如果你忘记了abstract的知识点，没关系我有[传送门](https://blog.csdn.net/pipizhen_/article/details/107207938)！），抽象工厂和工厂模式之间有什么异同等等问题

我们知道工厂是创建不同的商品的，一个形状工厂可以创建出圆形、正方形、椭圆形等等它本质上是一个工厂生产同类型的产品，而抽象工厂就是可以产生不同的工厂，你可以有形状工厂、颜色工厂等等。

我的理解是抽象工厂是对工厂的再封装，相当于在工厂模式的基础上的升级版本吧

我们还是先用[菜鸟教程](https://www.runoob.com/design-pattern/abstract-factory-pattern.html)的示例来理解这个概念，然后再去看小傅哥的示例

![抽象工厂模式的 UML 图](https://gitee.com/JongcyChen/PicBed/raw/master/img/3E13CDD1-2CD2-4C66-BD33-DECBF172AE03.jpg)

我们从`Shape`接口开始看，它依旧是可以画出三个形状，`ShapeFactory`就是我们的形状工厂

```java
//工厂模式：形状工厂
public class ShapeFactory {
   //使用 getShape 方法获取形状类型的对象
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
}
```

不同的是，它不仅有形状工厂，现在还加了颜色工厂，那么它们两个是怎么联系起来呢？就跟套娃一样，我再创建一个类，把这两个工厂的getShape和getColor给拿过来呗，我通过知道你想要颜色还是形状，要啥颜色啥形状，然后给你new对象就行啦!

下图就是创建的一个抽象工厂

```java
public abstract class AbstractFactory {
   public abstract Color getColor(String color);
   public abstract Shape getShape(String shape) ;
}
```

不同于工厂模式的是，我们的工厂需要继承我们定义好的抽象工厂，实现对应的方法：

```java
//抽象工厂：形状工厂
public class ShapeFactory extends AbstractFactory {
    
   @Override
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
   
   @Override
   public Color getColor(String color) {
      return null;
   }
}
```

**优点：** 

1、一个调用者想创建一个对象，只要知道其名称就可以了。

 2、扩展性高，如果想增加一个产品，只要扩展一个工厂类就可以。

 3、屏蔽产品具体实现，调用者只关心产品的接口。

**缺点：**每次增加一个产品时，都需要增加一个具体类和对象实现工厂，使得系统中类的个数成倍增加，在一定程度上增加了系统的复杂度，同时也增加了系统具体类的依赖。咱们可以小小算一下，如果现在在原有基础上，我不仅想要生产形状和颜色，我还想生产花纹，那么至少要增加三个类。那可想而知如果有十个产品就很多了....

那么抽象工厂具体的适用场景到底是什么呢？

小傅哥在文章中写下了言简意赅的一段话：

> 抽象工厂模式，所要解决的问题就是在一个产品族，存在多个不同类型的产品（Redis集群、操作系统）情况下，接口选择问题。而这种场景在业务开发中也是非常多见的，只不过可能有时候没有将它们抽象化出来。

书中的实例是**Redis**

![image-20210802203410563](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210802203410563.png)

随着业务超过预期快速发展，系统的负载能力也要随之更上，那么原有的单机Redis已经满足不了系统的需求。这个时候就需要更换为更为健壮的Redis集群服务，虽然需要修改但是不能影响目前系统的运行，还要平滑的过渡过去。

首先模拟了单机的Redis活动：

 **RedisUtils:**

模拟Redis功能，也就是假定目前所有的系统都在使用服务，类和方法名次都固定写死到各个业务系统中，改动略微麻烦

代码结构：

![image-20210802231315828](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210802231315828.png)

```java
public class RedisUtils {
    private Logger logger = LoggerFactory.getLogger(RedisUtils.class);

    private Map<String, String> dataMap = new ConcurrentHashMap<String, String>();

    public String get(String key) {
        logger.info("Redis获取数据 key：{}", key);
        return dataMap.get(key);
    }

    public void set(String key, String value) {
        logger.info("Redis写入数据 key：{} val：{}", key, value);
        dataMap.put(key, value);
    }

    public void set(String key, String value, long timeout, TimeUnit timeUnit) {
        logger.info("Redis写入数据 key：{} val：{} timeout：{} timeUnit：{}", key, value, timeout, timeUnit.toString());
        dataMap.put(key, value);
    }

    public void del(String key) {
        logger.info("Redis删除数据 key：{}", key);
        dataMap.remove(key);
    }
}
```

模拟集群 **EGM**:

```java
public class EGM {

    private Logger logger = LoggerFactory.getLogger(EGM.class);

    private Map<String, String> dataMap = new ConcurrentHashMap<String, String>();

    public String gain(String key) {
        logger.info("EGM获取数据 key：{}", key);
        return dataMap.get(key);
    }

    public void set(String key, String value) {
        logger.info("EGM写入数据 key：{} val：{}", key, value);
        dataMap.put(key, value);
    }

    public void setEx(String key, String value, long timeout, TimeUnit timeUnit) {
        logger.info("EGM写入数据 key：{} val：{} timeout：{} timeUnit：{}", key, value, timeout, timeUnit.toString());
        dataMap.put(key, value);
    }

    public void delete(String key) {
        logger.info("EGM删除数据 key：{}", key);
        dataMap.remove(key);
    }
}
```

会发现EGS有三个方法与RedisUtils不同：

![image-20210802230237552](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210802230237552.png)

再模拟集群 **IIR**：

```java
public class IIR {

    private Logger logger = LoggerFactory.getLogger(IIR.class);

    private Map<String, String> dataMap = new ConcurrentHashMap<String, String>();

    public String get(String key) {
        logger.info("IIR获取数据 key：{}", key);
        return dataMap.get(key);
    }

    public void set(String key, String value) {
        logger.info("IIR写入数据 key：{} val：{}", key, value);
        dataMap.put(key, value);
    }

    public void setExpire(String key, String value, long timeout, TimeUnit timeUnit) {
        logger.info("IIR写入数据 key：{} val：{} timeout：{} timeUnit：{}", key, value, timeout, timeUnit.toString());
        dataMap.put(key, value);
    }

    public void del(String key) {
        logger.info("IIR删除数据 key：{}", key);
        dataMap.remove(key);
    }

}
```

同样也有一个方法与RedisUtils类中不一样：`setExpire()`

综上可以看到，假设我们目前的系统中已经在大量的使用redis服务，但是因为系统不能满足业务的快速发展，因此需要迁移到集群服务中。而这时有两套集群服务需要兼容使用，又要满足所有业务系统的改造的同时不影响线上使用。

如果没有学习过设计模式，着实让人比较头疼呀！

那么小傅哥是怎么进行重构的呢？

首先，没有if else不能解决的逻辑，不行咱们就套娃！！！[狗头]我们对接口进行了改造：



![image-20210802231200310](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210802231200310.png)

这种方式很好理解，就是根据传进来的集群类型调用对应的方法，但是显然这种方法不是长久之计，并且还特别的笨拙，别人阅读这样的代码，会觉得这个程序员不太聪明的丫子，实际上我觉得我们要对自己写的代码有一个良好的质疑心态，当你发现重复的代码反复出现的时候，很可能就是代码的设计不够合理，看是否可以抽象或者重构。

```java
public class CacheServiceImpl implements CacheService {
    private RedisUtils redisUtils = new RedisUtils();
    private EGM egm = new EGM();
    private IIR iir = new IIR();
    public String get(String key, int redisType) {

        if (1 == redisType) {
            return egm.gain(key);
        }
        if (2 == redisType) {
            return iir.get(key);
        }
        return redisUtils.get(key);
    }

    public void set(String key, String value, int redisType) {

        if (1 == redisType) {
            egm.set(key, value);
            return;
        }
        if (2 == redisType) {
            iir.set(key, value);
            return;
        }
        redisUtils.set(key, value);
    }

    public void set(String key, String value, long timeout, TimeUnit timeUnit, int redisType) {

        if (1 == redisType) {
            egm.setEx(key, value, timeout, timeUnit);
            return;
        }
        if (2 == redisType) {
            iir.setExpire(key, value, timeout, timeUnit);
            return;
        }
        redisUtils.set(key, value, timeout, timeUnit);
    }

    public void del(String key, int redisType) {

        if (1 == redisType) {
            egm.delete(key);
            return;
        }
        if (2 == redisType) {
            iir.del(key);
            return;
        }
        redisUtils.del(key);
    }
}
```

<font color=red>✔️用抽象⼯厂模式来重构代码</font>

抽象工厂模型结构

![image-20210810194900536](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210810194900536.png)

代码结构：

![image-20210810195713754](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210810195713754.png)

工程中涉及的部分核心功能代码：

`ICacheAdapter`，定义了适配接口，分别包装两个集群中差异化的接口名称。`EGMCacheAdapter`、`IIRCacheAdapter`

`JDKProxy`、`JDKInvocationHandler`，是代理类的定义和实现，这部分也就是抽象工厂的另外一种实现方式。通过这样的实现方式可以很好的把握原有操作Redis的方法进行代理操作，通过控制不同的入参对象，控制缓存的使用。

**代码实现：**

1）定义适配接⼝： 这个类的主要作用是让所有集群的提供方，能在统一的方法名称下面进行操作，并且也方便后续的拓展。

```java
public interface ICacheAdapter {

    String get(String key);

    void set(String key, String value);

    void set(String key, String value, long timeout, TimeUnit timeUnit);

    void del(String key);

}

```

2）实现集群使⽤用服务：实现`ICacheAdapter`接口

以`EGMCacheAdapter`为例

```java
public class EGMCacheAdapter implements ICacheAdapter {

    private EGM egm = new EGM();

    public String get(String key) {
        return egm.gain(key);
    }

    public void set(String key, String value) {
        egm.set(key, value);
    }

    public void set(String key, String value, long timeout, TimeUnit timeUnit) {
        egm.setEx(key, value, timeout, timeUnit);
    }

    public void del(String key) {
        egm.delete(key);
    }
}
```

3）定义抽象工厂代理类和实现：

这里主要的作用是完成代理类，同时对于使用哪个集群由外部通过入参进行传递

```java
public class JDKProxy {

    public static <T> T getProxy(Class<T> interfaceClass, ICacheAdapter cacheAdapter) throws Exception {
        InvocationHandler handler = new JDKInvocationHandler(cacheAdapter);
        ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
        Class<?>[] classes = interfaceClass.getInterfaces();
        return (T) Proxy.newProxyInstance(classLoader, new Class[]{classes[0]}, handler);
    }

}
```

**JDKInvocationHandler**

```java
public class JDKInvocationHandler implements InvocationHandler {

    private ICacheAdapter cacheAdapter;

    public JDKInvocationHandler(ICacheAdapter cacheAdapter) {
        this.cacheAdapter = cacheAdapter;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return ICacheAdapter.class.getMethod(method.getName(), ClassLoaderUtils.getClazzByArgs(args)).invoke(cacheAdapter, args);
    }

}
```



### 三、建造者模式（<font color=red>Builder Pattern</font>）

**建造者模式（Builder Pattern）**使用多个简单的对象一步一步构建成一个复杂的对象。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

一个 Builder 类会一步一步构造最终的对象。该 Builder 类是独立于其他对象的

应用实例：

 1、去肯德基，汉堡、可乐、薯条、炸鸡翅等是不变的，而其组合是经常变化的，生成出所谓的"套餐"。 

 2、JAVA 中的 StringBuilder。

3、房屋的装修，装修步骤是一样的，但是装修过程中选择不同的组合会有不同的风格。

![image-20210820165005713](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210820165005713.png)

**建造者模式所完成的的内容就是通过将多个简单对象通过一步步的组装构建出一个复杂对象的过程。**

文章中有一段话我觉得将建造者模式讲的非常清楚：

![image-20210821102131749](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210821102131749.png)

**案例场景模拟：**

我们模拟装修公司对于设计出一些套餐装修服务的场景。

将装修步骤简化为以下四个。

![image-20210821102625412](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210821102625412.png)

很多装修公司都会给出自家的套餐服务，一般有：欧式豪华、轻奢田园、现代简约等等，而这些套餐的后面是不同商品的组合。

例如吊顶有一级&二级；涂料有多乐士涂料；地板有圣像地板；马可波罗地砖等等，按照不同的套餐架构选取不同的品牌组合，最终再按照装修面积给出一个整理报价。

这里我们就模拟装修公司想推出一些套餐装修服务，按照不同的价格设定品牌选择组合，以达到使用建造者模式的过程。

![image-20210821111315739](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210821111315739.png)

在模拟工程中提供了装修中所需要的物料如上图四项内容；

**Matter**是物料接口，其他的物料均实现这个接口，以保证所有的装修材料都可以按照统一标准进行获取。

```java
/**
 * 装修物料
 */
public interface Matter {

    /**
     * 场景；地板、地砖、涂料、吊顶
     */
    String scene();

    /**
     * 品牌
     */
    String brand();

    /**
     * 型号
     */
    String model();

    /**
     * 平米报价
     */
    BigDecimal price();

    /**
     * 描述
     */
    String desc();
}
```

**吊顶Ceiling：**

```java
/**
 * 吊顶
 * 品牌；装修公司自带
 * 型号：一级顶
 */
public class LevelOneCeiling implements Matter {

    public String scene() {
        return "吊顶";
    }

    public String brand() {
        return "装修公司自带";
    }

    public String model() {
        return "一级顶";
    }

    public BigDecimal price() {
        return new BigDecimal(260);
    }

    public String desc() {
        return "造型只做低一级，只有一个层次的吊顶，一般离顶120-150mm";
    }

}
```

**涂料(coat):**

```java
/**
 * 涂料
 * 品牌；多乐士(Dulux)
 */
public class DuluxCoat  implements Matter {

    public String scene() {
        return "涂料";
    }

    public String brand() {
        return "多乐士(Dulux)";
    }

    public String model() {
        return "第二代";
    }

    public BigDecimal price() {
        return new BigDecimal(719);
    }

    public String desc() {
        return "多乐士是阿克苏诺贝尔旗下的著名建筑装饰油漆品牌，产品畅销于全球100个国家，每年全球有5000万户家庭使用多乐士油漆。";
    }
    
}
```

地板和地砖也是类似，这里就不再列出来了

那么接下来要通过上面这个案例去使用不同的物料组合，组合出不同的套餐服务

❌**用一坨坨代码实现**

```java
public class DecorationPackageController {

    public String getMatterList(BigDecimal area, Integer level) {

        List<Matter> list = new ArrayList<Matter>(); // 装修清单
        BigDecimal price = BigDecimal.ZERO;          // 装修价格

        // 豪华欧式
        if (1 == level) {

            LevelTwoCeiling levelTwoCeiling = new LevelTwoCeiling(); // 吊顶，二级顶
            DuluxCoat duluxCoat = new DuluxCoat();                   // 涂料，多乐士
            ShengXiangFloor shengXiangFloor = new ShengXiangFloor(); // 地板，圣象

            list.add(levelTwoCeiling);
            list.add(duluxCoat);
            list.add(shengXiangFloor);

            price = price.add(area.multiply(new BigDecimal("0.2")).multiply(levelTwoCeiling.price()));
            price = price.add(area.multiply(new BigDecimal("1.4")).multiply(duluxCoat.price()));
            price = price.add(area.multiply(shengXiangFloor.price()));

        }

        // 轻奢田园
        if (2 == level) {

            LevelTwoCeiling levelTwoCeiling = new LevelTwoCeiling(); // 吊顶，二级顶
            LiBangCoat liBangCoat = new LiBangCoat();                // 涂料，立邦
            MarcoPoloTile marcoPoloTile = new MarcoPoloTile();       // 地砖，马可波罗

            list.add(levelTwoCeiling);
            list.add(liBangCoat);
            list.add(marcoPoloTile);

            price = price.add(area.multiply(new BigDecimal("0.2")).multiply(levelTwoCeiling.price()));
            price = price.add(area.multiply(new BigDecimal("1.4")).multiply(liBangCoat.price()));
            price = price.add(area.multiply(marcoPoloTile.price()));

        }

        // 现代简约
        if (3 == level) {

            LevelOneCeiling levelOneCeiling = new LevelOneCeiling();  // 吊顶，二级顶
            LiBangCoat liBangCoat = new LiBangCoat();                 // 涂料，立邦
            DongPengTile dongPengTile = new DongPengTile();           // 地砖，东鹏

            list.add(levelOneCeiling);
            list.add(liBangCoat);
            list.add(dongPengTile);

            price = price.add(area.multiply(new BigDecimal("0.2")).multiply(levelOneCeiling.price()));
            price = price.add(area.multiply(new BigDecimal("1.4")).multiply(liBangCoat.price()));
            price = price.add(area.multiply(dongPengTile.price()));
        }

        StringBuilder detail = new StringBuilder("\r\n-------------------------------------------------------\r\n" +
                "装修清单" + "\r\n" +
                "套餐等级：" + level + "\r\n" +
                "套餐价格：" + price.setScale(2, BigDecimal.ROUND_HALF_UP) + " 元\r\n" +
                "房屋面积：" + area.doubleValue() + " 平米\r\n" +
                "材料清单：\r\n");

        for (Matter matter: list) {
            detail.append(matter.scene()).append("：").append(matter.brand()).append("、").append(matter.model()).append("、平米价格：").append(matter.price()).append(" 元。\n");
        }

        return detail.toString();

    }
}
```

测试验证：

```java
public class ApiTest {

    @Test
    public void test_DecorationPackageController(){
        DecorationPackageController decoration = new DecorationPackageController();

        // 豪华欧式
        System.out.println(decoration.getMatterList(new BigDecimal("132.52"),1));

        // 轻奢田园
        System.out.println(decoration.getMatterList(new BigDecimal("98.25"),2));

        // 现代简约
        System.out.println(decoration.getMatterList(new BigDecimal("85.43"),3));
    }

}
```

测试结果：

```
-------------------------------------------------------
装修清单
套餐等级：1
套餐价格：198064.39 元
房屋面积：132.52 平米
材料清单：
吊顶：装修公司自带、二级顶、平米价格：850 元。
涂料：多乐士(Dulux)、第二代、平米价格：719 元。
地板：圣象、一级、平米价格：318 元。

-------------------------------------------------------
装修清单
套餐等级：2
套餐价格：119865.00 元
房屋面积：98.25 平米
材料清单：
吊顶：装修公司自带、二级顶、平米价格：850 元。
涂料：立邦、默认级别、平米价格：650 元。
地砖：马可波罗(MARCO POLO)、缺省、平米价格：140 元。

-------------------------------------------------------
装修清单
套餐等级：3
套餐价格：90897.52 元
房屋面积：85.43 平米
材料清单：
吊顶：装修公司自带、一级顶、平米价格：260 元。
涂料：立邦、默认级别、平米价格：650 元。
地砖：东鹏瓷砖、10001、平米价格：102 元。
```

看到这个输出的结果，已经很有装修公司提供报价单的感觉了。以上是使用`ifelse`方式实现的代码，目前只是有三种装修风格，但是随着老板对业务的快速发展要求，会提供很多的套餐针对不同的户型。那么就得不停的在这个`getMatterList`方法中增加逻辑，于是这个方法会越来越大，越来越难以维护了。

✔️**建造者模式重构代码**

建造者模式主要解决的问题是在软件系统中，有时候面临着“一个复杂对象”的创建工作，其通常由各个部分的子对象用一定的过程构成；由于需要的变化，这个复杂对象的各个部分经常面临着重大的变化，但是将它们组合在一起的过程却相对稳定。

这里我们会把构建的过程交给`创建者`类，而创建者通过使用我们的构建工具包，去构建出部门的"装修套餐"。

**建造者的模型结构：**

![image-20210822165427435](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210822165427435.png)

**工程结构：**

![image-20210822165932321](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210822165932321.png)

`Builder`，建造者类具体的各种组装由此类实现

`DecorationPackageMenu`是`IMenu`接口的实现类，主要是承载建造过程中的填充器。相当于这是一套承载物料和创建者中间的衔接内容。

**`IMenu`**

```java
public interface IMenu {

    /**
     * 吊顶
     */
    IMenu appendCeiling(Matter matter);

    /**
     * 涂料
     */
    IMenu appendCoat(Matter matter);

    /**
     * 地板
     */
    IMenu appendFloor(Matter matter);

    /**
     * 地砖
     */
    IMenu appendTile(Matter matter);

    /**
     * 明细
     */
    String getDetail();

}
```

**`DecorationPackageMenu`**

```java
/**
 * 装修包
 */
public class DecorationPackageMenu implements IMenu {

    private List<Matter> list = new ArrayList<Matter>();  // 装修清单
    private BigDecimal price = BigDecimal.ZERO;      // 装修价格

    private BigDecimal area;  // 面积
    private String grade;     // 装修等级；豪华欧式、轻奢田园、现代简约

    private DecorationPackageMenu() {
    }

    public DecorationPackageMenu(Double area, String grade) {
        this.area = new BigDecimal(area);
        this.grade = grade;
    }

    public IMenu appendCeiling(Matter matter) {
        list.add(matter);
        price = price.add(area.multiply(new BigDecimal("0.2")).multiply(matter.price()));
        return this;
    }

    public IMenu appendCoat(Matter matter) {
        list.add(matter);
        price = price.add(area.multiply(new BigDecimal("1.4")).multiply(matter.price()));
        return this;
    }

    public IMenu appendFloor(Matter matter) {
        list.add(matter);
        price = price.add(area.multiply(matter.price()));
        return this;
    }

    public IMenu appendTile(Matter matter) {
        list.add(matter);
        price = price.add(area.multiply(matter.price()));
        return this;
    }

    public String getDetail() {

        StringBuilder detail = new StringBuilder("\r\n-------------------------------------------------------\r\n" +
                "装修清单" + "\r\n" +
                "套餐等级：" + grade + "\r\n" +
                "套餐价格：" + price.setScale(2, BigDecimal.ROUND_HALF_UP) + " 元\r\n" +
                "房屋面积：" + area.doubleValue() + " 平米\r\n" +
                "材料清单：\r\n");

        for (Matter matter: list) {
            detail.append(matter.scene()).append("：").append(matter.brand()).append("、").append(matter.model()).append("、平米价格：").append(matter.price()).append(" 元。\n");
        }

        return detail.toString();
    }

}
```

**`Builder`**

```java
public class Builder {

    public IMenu levelOne(Double area) {
        return new DecorationPackageMenu(area, "豪华欧式")
                .appendCeiling(new LevelTwoCeiling())    // 吊顶，二级顶
                .appendCoat(new DuluxCoat())             // 涂料，多乐士
                .appendFloor(new ShengXiangFloor());     // 地板，圣象
    }

    public IMenu levelTwo(Double area){
        return new DecorationPackageMenu(area, "轻奢田园")
                .appendCeiling(new LevelTwoCeiling())   // 吊顶，二级顶
                .appendCoat(new LiBangCoat())           // 涂料，立邦
                .appendTile(new MarcoPoloTile());       // 地砖，马可波罗
    }

    public IMenu levelThree(Double area){
        return new DecorationPackageMenu(area, "现代简约")
                .appendCeiling(new LevelOneCeiling())   // 吊顶，二级顶
                .appendCoat(new LiBangCoat())           // 涂料，立邦
                .appendTile(new DongPengTile());        // 地砖，东鹏
    }

}
```

`APITest`

```java
public class ApiTest {

    @Test
    public void test_Builder(){
        Builder builder = new Builder();

        // 豪华欧式
        System.out.println(builder.levelOne(132.52D).getDetail());

        // 轻奢田园
        System.out.println(builder.levelTwo(98.25D).getDetail());

        // 现代简约
        System.out.println(builder.levelThree(85.43D).getDetail());
    }

}
```

测试结果是一样的，调用方式也基本类似。但是目前的代码结构却可以让你很方便很由条理的进行业务扩展开发。而不是以往一样把所有代码都写到`ifelse`里面。

通过上面对建造者模式的使用，可以得出，**当一些基本物料不会变，而其组合经常变化的时候**，就可以采用这样的设计模式来构建代码。

词设计模式满足了单一职责原则以及可复用的技术、建造者独立、易扩展、便于控制细节风险。但同时当出现特别多的物料以及很多的组合后，**类的不断扩展也会造成难以维护的问题**。但这种设计结构模型可以把重复的内容抽象到数据库中，按照需要配置。这样就可以减少代码中出现大量的重复。

### 四、原型模式（<font color=red>Prototype Pattern</font>）

原型模式主要解决的问题就是创建重复对象，而这部分对象内容本身比较复杂，生产过程可能从库或者RPC接口中获取数据的耗时较长，因此采用克隆的方式节省时间。

这种场景经常出现在我们身边，例如：

1. `ctrl+C` 、`ctrl-V`复制粘贴
2. Java多数类中提供的API方法；Object clone()
3. 细胞的有丝分裂
4. ......

**案例场景模拟：**

文章给出的例子是实现一个上机考试抽题的服务，因此在这里建造了一个题库题目的场景类ixnx，用于创建；包括选择题和问答题。

![image-20210822211902185](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210822211902185.png)

选择题：

```java
/**
 * 单选题
 */
public class ChoiceQuestion {

    private String name;                 // 题目
    private Map<String, String> option;  // 选项；A、B、C、D
    private String key;                  // 答案；B

    public ChoiceQuestion() {
    }

    public ChoiceQuestion(String name, Map<String, String> option, String key) {
        this.name = name;
        this.option = option;
        this.key = key;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Map<String, String> getOption() {
        return option;
    }

    public void setOption(Map<String, String> option) {
        this.option = option;
    }

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }
}

```

填空题：

```java
/**
 * 解答题
 */
public class AnswerQuestion {

    private String name;  // 问题
    private String key;   // 答案

    public AnswerQuestion() {
    }

    public AnswerQuestion(String name, String key) {
        this.name = name;
        this.key = key;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }
}
```

❌**用一坨坨代码实现**

首先我们还是用`ifelse`来实现







### 五、单例模式（<font color=red>Singleton Pattern</font>）





## 结构型模式<font color=green>（7节）</font>

