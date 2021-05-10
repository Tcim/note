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
|-|-|-|
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