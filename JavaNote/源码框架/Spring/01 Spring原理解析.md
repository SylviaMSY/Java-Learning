

## Bean的创建生命周期

UserService.class ---> 无参构造方法（推断找出构造方法）--> 普通对象-->依赖注入（属性赋值）-->初始化前（@PostConstruct）-->初始化（initilaizingBean）-->初始化后（判断是否AOP）-->代理对象-->Bean



## AOP

``` java
class UserServiceProxy extends UserService{
    
    UserService target;//获得UserService的普通对象（通过依赖注入有值）
    public void test(){
        //执行@Before
        
        //super.test()
        target.test();
    }
}
```

**代理对象本身是没有值得（不会进行依赖注入），而是通过在类中定义属性（UserService对象值，是依赖注入有值的）**



**利用cglib进行AOP的大致流程：** 

1. 生成代理类UserServiceProxy，代理类继承UserService 
2. 代理类中重写了父类的方法，比如UserService中的test()方法 
3. 代理类中还会有一个target属性，该属性的值为被代理对象（也就是通过 UserService类推断构造方法实例化出来的对象，进行了依赖注入、初始化等步骤的 对象）
4. 代理类中的test()方法被执行时的逻辑如下：
   - 执行切面逻辑（@Before） 
   - 调用target.test()



**如何判断当前Bean对象需不需要进行AOP:** 

1. 找出所有的切面Bean 
2. 遍历切面中的每个方法，看是否写了@Before、@After等注解 
3. 如果写了，则判断所对应的Pointcut是否和当前Bean对象的类是否匹配 
4. 如果匹配则表示当前Bean对象有匹配的的Pointcut，表示需要进行AOP



## Spring事务



``` java
class UserServiceProxy extends UserService{
    
    UserService target;
    public void test(){
        //1.@Transa 加了事务注解
        //2.创建一个数据库连接conn（由事务管理器dataSource创建）
        //3.conn.autocimmit = false;(关闭事务自动提交)
        target.test();//sql1,sql2
        
        conn.commit();//提交事务
        conn.rollback();//或有异常则回滚
    }
}
```

