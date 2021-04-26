## 3. 模板方法模式(Template Method)
### 3.1 definition:
在父类中定义处理流程的框架, 在子类中进行实现具体处理过程的模式称为模板方法模式(Template Method pattern).
### 3.2 类图
  ![](https://i.loli.net/2021/04/26/FhmPW6wLv2dT3Ee.png)
**AbstractClass:** 抽象类; 负责实现模板方法, 并生命在模板方法中使用到的抽象方法. 这些抽象方法由其子类负责实现.
**ConcreteClass:** 具体类; 负责实现`AbstractClass`中定义的抽象方法.
最终, 客户端调用的是`templateMethod`方法. 在`templateMethod`方法中, 定义好了目标工作的完整处理逻辑, 只是其中调用的抽象方法由其子类来实现.
## 4. 工厂方法(Factory Method)
## 5. 单例模式(Singleton)
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