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
## 9 Bridge Pattern (桥接模式)
### 9.1 definition
- 类的功能层次结构
  类的功能层次结构指在类的继承关系中子类对父类的功能的扩充. 每当要在一个类上增加新的功能时, 我们就编写一个子类继承它, 完成新功能的增加. 随着继承的层数增加, 类的功能也越来越多. 这种层次结构我们成为类的功能层次结构.
- 类的实现层次结构
  在[模板方法模式](#3-模板方法模式template-method)中, 父类通过声明抽象方法来定义接口, 子类通过实现具体方法来实现接口. 不同的子类对父类的接口有不同的实现. 但是这里类的继承并不是为了增加新的功能, 只是对父类声明的功能做具体实现. 这种继承(实现)关系所组成的类的层次结构, 我们称之为**类的实现层次结构**.

从这两个层次结构的定义来看, 一个类的继承结构中是**可能同时存在功能层次结构和实现层次结构**的, 例如在实现父类抽象方法的同时又要添加新的功能(函数). 这样这两种结构混杂就在混杂在了一起, 形成了强耦合的关系. 如又有新的实现方法, 但是功能不变时, 则需要重新实现的同时, 再次添加同样的功能.

针对这一问题, 我们采用**桥接模式**将类的功能层次机构和类的实现层次结构分离为两个独立的类层次结构.
### 9.2 类图
![桥接器模式类图](https://i.loli.net/2021/05/10/Wzt5GVXNeHSwvBr.png)
- `Abstraction`: 类的功能层次的最上层.
- `Implementor`: 类的实现层次的最上层, 聚合于`Abstraction`中, 为其基本功能实现定义调用接口.
- `ConcreteImplementor`: 具体实现类, 实现`Implementor`定义的方法接口.
- `RefinedAbstraction`: 继承`Abstraction`, 实现功能扩充, 可以调用`Implementor`的方法接口.
### 9.3 桥接模式的优点
- 方便功能扩展. 由于两个层次结构的分离, 我们可以方便地对两个层次结构进行扩展或重实现, 而不用考虑和对方结构的关系. 
## 10 Strategy Pattern (策略模式)
### 10.1 definition
策略模式的主要关注点是"策略", 是对具体的方法(算法)所进行的抽象. Strategy模式可以灵活地替换掉算法的不同实现, 而保持其他结构不变.
### 10.2 类图
![策略模式类图](https://i.loli.net/2021/05/10/LmhFZtCJesS9nRE.jpg)
- `Context`: 策略的使用者
- `Strategy`: 抽象策略, 定义实现策略的接口.
- `ConcreteStrategy`: 具体策略. 实现`Strategy`定义的接口.
## 11 Composite Pattern (组合模式)
### 11.1 definition
容器和内容往往是不一致的, 容器中包含内容, 二者表示不同的特征. 但有时, 我们需要将容器和内容相同看待. 如文件系统的目录结构中, 文件夹于单个文件虽然有包含关系, 但二者又在某种程度上是同一种类型, 特别是使用例如`ls`命令时, 文件夹于与文件都以条目的形式展现出来, 而且他们具有一些相同的属性, 如访问权限, 所有者或所属用户组等.

这种创造容器与内容的一致性的模式就是**组合模式**. 组合模式创造的结构时递归的.
### 11.2 类图
![组合模式类图](https://i.loli.net/2021/05/10/i6mz95vtWGKTFVY.png)
- `Component`: 抽象的一致性角色.
- `Leaf`: 内容的角色.
- `Composite`: 复合对象, 继承`Component`, 同时提供容器存放component, 但同时其本身也是一个`Component`. 
## 12 Decorator Pattern (装饰器模式)
### 12.1 definition

