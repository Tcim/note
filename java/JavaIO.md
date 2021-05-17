# Stream IO
## 1. 字节流
### 1.1 输入流类型
输入流`InputStream`表示从不同数据源产生输入的类, 不同数据源使用不同的子类实现. 数据源类型包含:
- 字节数组
- String对象
- 文件
- Pipe
- 其他种类的流组成的序列
- 其他数据源, 如Internet连接

不同的数据源类型的输入流子类的继承关系为:
  ![](https://i.loli.net/2021/05/08/d1VxgYJKAOnRas3.png)
输入流的类和功能如表:

|类|功能|构造参数|
|--|--|--|
|FileInputStream|从文件中读入字节流|String(文件名)|
|ByteArrayInputStream|从字节数组(内存的缓冲区)读入字节流|缓冲区, 字节数组|
|PipedInputStream|产生用于写入相关PipedOutputStream的数据|PipedOutputStream|

`FilterInputStream`是一个抽象类, 用来提供装饰器类接口以控制特定的输入流. `FilterInputStream`类的实现类提供以下功能:

|类|功能|构造器参数|
|--|--|--|
|DataInputStream|从输入流读取基本数据类型和String类型对象, 使用`readByte()`, `readInteger()`等方法|InputStream|
|BufferedInputStream|对输入流添加默认的缓冲功能, 支持`mark`和`reset`方法|InputStream|
|PushbackInputStream|具有能弹出一个字节的缓冲区, 可以将读到的最后一个字节回退|InputStream|

### 1.2 输出流类型
同输入流一样, 输出流也有相应的类型. 包含:
- 字节数组
- 文件
- 管道
  
不同数据类型的输出流继承关系:

输出流的类和功能:
|类|功能|构造参数|
|--|--|--|
|ByteArrayOutputStream|内存缓冲区, 流中的数据放在缓冲区|内存缓冲区(字节数组)|
|FileOutputStream|将数据流写入文件|字符串, 表示文件名|
|PipedOutputStream|写入其中的信息会自动作为相关PipedInputStream的输出|PipedInputStream|

`FilterOutputStream`同`FilterInputStream`一样, 是一个抽象类, 用来提供装饰器类接口以定制化特定的输出流. 有如下具体的装饰类:
|类|功能|构造参数|
|--|--|--|
|DataOutputStream|将各种基本数据类型和String类型的对象格式化输出到流中. 使用`writeByte()`, `writeFloat()`等.|OutputStream|
|PrintStream|用于格式化输出, 以可视化格式打印所有基本数据类型和String类型的对象. 包含`print()`和`pringln`方法|OutputStream|
|BufferedOutputStream|避免每次发送数据都进行实际的写操作. 可以调用`flush()`函数清空缓冲区.| OutputStream, 可选缓冲区大小|

## 2 字符流
字符流`Reader`和`Writer`提供兼容**Unicode**编码的面向字符的I/O功能. 设计字符流的**动机是处理字节流仅支持8bit的ASCII字符, 对16bit的Unicode字符处理好**.
### 2.1 基础字符流
同字节流类似, 字符流有着高度相似的继承体系.
- `Reader`系列:
  ![](https://i.loli.net/2021/05/17/Bej7RJmQc4iqEZu.png)
- `Writer`系列:
  ![](https://i.loli.net/2021/05/17/UgSA2XcsG7LRump.png)