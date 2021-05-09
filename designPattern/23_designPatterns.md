## 3 模板方法模式(Template Method)
### 3.1 definition:
在父类中定义处理流程的框架, 在子类中进行实现具体处理过程的模式称为模板方法模式(Template Method pattern).
### 3.2 类图
  ![](https://i.loli.net/2021/04/26/FhmPW6wLv2dT3Ee.png)

**AbstractClass:** 抽象类; 负责实现模板方法, 并生命在模板方法中使用到的抽象方法. 这些抽象方法由其子类负责实现.
**ConcreteClass:** 具体类; 负责实现`AbstractClass`中定义的抽象方法.
最终, 客户端调用的是`templateMethod`方法. 在`templateMethod`方法中, 定义好了目标工作的完整处理逻辑, 只是其中调用的抽象方法由其子类来实现.
## 4 工厂方法(Factory Method)
## 5 单例模式(Singleton)
### 5.1 definition:
单例模式确保一个类只能生成一个实例.
### 5.2 类图
  ![](https://i.loli.net/2021/04/26/tvUdGgEiZFyKkPW.png)

**`Singleton`类的构造函数是private的, 主要为了禁止从`Singleton`类外调用构造方法.**
### 5.3 单例模式的实现方法:
#### 5.3.1 懒汉模式
```java
//线程不安全
public class Singleton{
    private static Singleton singleton;
    private Singleton(){}
    public static Singleton getInstance(){
        if(singleton == null){
            singleton = new Singleton();
        }
        return singleton;
    }
}
//线程安全, 使用synchronized修饰方法
public class Singleton{
    private static Singleton singleton;
    private Singleton(){}
    public static synchronized Singleton getInstance(){
        if(singleton == null){
            singleton = new Singleton();
        }
        return singleton;
    }
}
//双重校验法, 通常情况下是线程安全的, 由于指令优化重排的因素, 由概率出现线程不安全
public class Singleton{
    private static Singleton singleton;
    private Singleton(){}
    public static Singleton getInstance(){
        if(singleton == null){
            synchronized(singleton){
                if(singleton == null){
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
//使用volatile关键字, 禁止指令重排
public class Singleton{
    private static volatile Singleton singleton;
    private Singleton(){}
    public static Singleton getInstance(){
        if(singleton == null){
            synchronized(singleton){
                if(singleton == null){
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```
#### 5.3.2 饿汉模式
```java
//线程安全, 类加载时生成实例
public class Singleton{
    private static Singleton singleton = new Singleton();
    private Singleton(){}
    public static Singleton getInstance(){
        return singleton;
    }
}
```
## 6 Prototype Pattern (原型模式)
### 6.1 definition
在大多数情况下, 我们创建实例时使用`new`关键字直接实例化对象, 这种方式下, 我们需要使用完全限定的类名. 但有些情况下, 我们并不一定需要使用类来实例化对象, 如:
- 需要处理的对象太多
- 难以根据类来生成实例, 比如类的实例化过程太复杂.
- 想要解耦框架与生成实例的过程, 在框架中, 一旦我们使用确定的类名来实例化对象时, 则意味着改框架与生成的类紧密地耦合在了一起.

在这些情况下, 我们可以使用原型模式来获得我们想要的对象. 原型模式的思路非常简单, 即预先生成需要的对象并统一存放, 在需要使用某一类的对象时, 则直接获得已有的对象复制品.
### 6.2 类图
![原型模式类图](https://i.loli.net/2021/04/28/pUd68Og7bBEDaPJ.png)

- `Prototype`: 负责定义用于复制现有实例来生成新实例的方法.
- `ConcretePrototype`: 负责实现复制现有实例并生成新实例的方法.
- `Manager`: 负责使用复制实例的方法生成新的实例.
### 6.3 Java语言的`clone`方法和`Clonable`接口
Java语言提供了复制实例的clone方法, 是`Object`类的基本方法. 但是, 若要调用对象的`clone`方法, 则类或父类必须实现了`java.lang.Clonable`接口, 该接口没有任何方法, 是**标记接口**. 如果没有实现`java.lang.Cloneable`接口, 则调用`clone`方法会抛出`CloneNotSupportException`异常.
> `clone`方法是浅拷贝, 它会讲原型对象中字段的值直接赋值给复制对象, 意味着如果字段是引用类型, 则复制的仅为一个引用.

## 7 Builder Pattern (建造者模式)
### 7.1 definition
在[模板方法模式](#3-模板方法模式template-method)中, 抽象父类通过抽象方法来定义了子类的行为. 其父类虽然不实现具体的操作步骤的方法, 但规定了方法的调用步骤.

但在一些情况下, 虽然具体的操作步骤是固定的, 但其执行的顺序并不是完全固定的. 也就是说, 不存固定的**模板方法**, 仅存在固定的**步骤**.

在这总情况下, 就需要使用到**建造者模式**. 在建造模式中, 建造者类定义了组件的组成部分, 而具体的组装部分, 则有一个第三方的类来具体实施.
### 7.2 类图
![建造者模式类图](https://i.loli.net/2021/04/28/PJeET9Kxu31ycID.png)
- `Builder`: 建造者, 负责定义各组件的接口.
- `ConcreteBuilder`: 具体的建造者, 负责实现`Builder`接口的方法. 
- `Director`: 指挥者. 负责调用建造者提供的方法, 最终构建出目标. `Director`不依赖于`ConcreteBuilder`类, 仅调用`Builder`接口提供的方法. 不同的Director对构建的方法不同.
## 8 Abstract Factory Pattern (抽象工厂模式)
### 8.1 definition
*工厂是将零件组装成产品的地方*

抽象工厂模式中, 我们关注**抽象零件**, **抽象产品**和**抽象工厂**. 抽象工厂的工作是将抽象零件组装成为抽象产品.

抽象工厂模式中, 我们不关心零件的具体实现, 只关注的接口的定义.
### 8.2 类图
![抽象通常类图](https://i.loli.net/2021/05/09/VaYbm4lTXr3FSuE.jpg)
具体工厂实现抽象工厂, 实现了组件的创建. 具体的组件类实现抽象的组件类, 赋予抽象组件以实际的功能.
### 8.3 抽象工厂和建造者模式的区别
抽象工厂模式和建造者模式的主要区别在于关注的东西不同. 抽象工厂关注的是产品的整体, 不需要关注零件的装配顺序. 而建造者模式的关注点在于组装的过程.

## 12 Decorator Pattern (装饰器模式)
### 12.1 definition
