#### Tomcat类加载器：打破双亲委派

AppClassLoader

-->Tomcat类加载器（common、server、shared文件）

-->Tomcat类加载器（WebappClassLoader：每个war包有自己的独立实例）

-->Tomcat类加载器（Jsp类加载器）



#### JVM整体结构及内存模型

![image-20210729215205126](C:\Users\MSY\AppData\Roaming\Typora\typora-user-images\image-20210729215205126.png)



##### 栈：存放方法的信息，包括局部变量表、操作数栈、动态链接、方法出口

- 栈（线程）：包括main()方法、用户方法
- 本地方法栈：GC垃圾回收等

##### 堆：存放对象本身

- 对象本身

##### 方法区（元空间）：符号引用，存放堆内对象的地址

- 常量
- 静态变量
- 类对象等类信息