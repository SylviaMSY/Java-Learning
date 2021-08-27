## JVM类加载全过程

#### java命令执行过程：

- c++：java指令，java.exe调用底层的jvm.dll文件创建java虚拟机（c++实现）
- c++：创建一个引导类加载器实例
- c++：调用java代码，创建JVM启动器实例sum.misc.lancher
- sum.misc.lancher.getLaunCher()
- launcher.getClassLoader()
- **classLoader.loadClass("com.mycompany.jvm.类名")**
- 类.main()

#### loadClass的类加载过程：

加载 >> 验证 >> 解析 >> 初始化

- 加载
- 验证
- 解析
- 初始化
  - static变量、static代码块
  - 构造器



#### 类加载机制和双亲委派机制

> ClassLoader类主要有两个核心方法：
>
> 1. loadClass（String, boolean）：选择加载器（实现了双亲委派机制）
> 2. findClass()：默认空实现，throw new ClassNotFoundException(name)

##### 类加载机制

- 引导类加载器（null：c++生产的对象）：加载JRE的lib目录下的核心jar类库
- 扩展类加载器（ExtClassLoader）：加载JRElib-ext目录下的扩展jar类
- 应用程序类加载器（AppClassLoader）：加载classbean目录下
- 自定义加载器：

##### 双亲委派机制（类加载器之间的关系，由loadClass()方法实现）

<img src="C:\Users\MSY\AppData\Roaming\Typora\typora-user-images\image-20210728195754182.png" alt="image-20210728195754182" style="zoom:67%;" />

##### 双亲委派机制作用

- 沙箱安全机制：避免用户定义的类与JVM中的类重名，防止JVM核心类被篡改
- 避免重复加载：
  - 若当前加载器中没有已加载，则向上委托父类加载器查询是否有已加载的
  - 父类中都没有，则开始加载工作，查询是否属于自身加载范围，若不属于， 则向下由子加载器加载



##### 自定义类加载器（父加载器为AppClassLoader）

- 继承ClassLoader类
- 重写findClass()方法

##### 打破双亲委派机制（在自定义类加载器的基础上）

- 改写loadClass()方法：自定义加载器的选择、以及加载路径。

