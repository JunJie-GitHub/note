# Java基础(侧重应用)

## 1.动态代理

**Cglib代理**实现: new 一个EnHancer对象, 调用setSuperClass()方法设置目标类, 再调用setCallBack方法传入MethodInterceptor接口实现类, 在intercept方法里面写代理逻辑.

```java
    final Handler target = new Handler();
    Enhancer enhancer = new Enhancer();
    enhancer.setSuperclass(Handler.class);
    enhancer.setCallback(new MethodInterceptor() {
        @Override
        public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
            System.out.println("代理方法前");
            Object o1 = method.invoke(target);
            System.out.println("代理方法后");
            return o1;
        }
    });
    Object o = enhancer.create();
    ((Handler) o).handle();
```

**JDK代理**实现: 待用Proxy.newProxyInstance()方法, 传入目标接口的ClassLoader和InvacationHandler接口实现类, 在实现类的invoke方法里写代理逻辑,返回的是代理类对象

```java
    final Handler target = new Handler();
    Object o = Proxy.newProxyInstance(IHandler.class.getClassLoader(), new Class[]{IHandler.class}, new InvocationHandler() {
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println("代理方法前");
            Object o1 = method.invoke(target, args);
            System.out.println("代理方法后");
            return o1;
        }
    });
    if (o instanceof IHandler){
        ((IHandler)o).handle();
    }
```

源码: 待补充

## 2.开发中用过的设计模式

单例模式: 用得最多, 对象要是没有保存数据的功能, 只需要调用里面的方法, 用的基本是单例模式

代理模式: 需要对一个功能进行增强, 又不想修改原有的方法, 可能原本的方法还会用到, 就会用代理模式

建造者模式: 尝试用过, 在bean里面建一个builder方法可以用来给属性赋值, 代码看起来更加简洁, 对象属性很多的时候就不用一堆setter方法来赋值,看起来很乱, lombok里提供了@builder注解实现代理模式

装饰者模式: 装饰者模式就是定义很多装饰器, 继承于同一个接口, 调用一个方法的时候根据装饰器的不同实现不一样的代码逻辑, 动态地扩展实现类的功能. 我写过一个xml解析类, 传入一个HashMap, 存的是对象属性跟xml的对应关系, mybatis的resultMap标签的对象关系映射, key是属性名, value是xml的解析公式, 又传入类.class和xml字符串. 用的SAXReader获取xml元素值, 用反射判断属性类型并赋值. 可以认为这个hashmap就是一个装饰器, 闯入不同的hashmap就可以实现不一样的处理  



## 1.接口实现文件的下载上传



# SpringMVC



# SpringBoot



# 数据库

## 1.sql优化

### 	insert语句优化:

#### 		1.合并多条语句为一条, 即insert into t values(a,b,c),(d,e,f)...

​		因为多条insert合并后的日志量减少了(MySQL的binlon和Innodb的事务日志), 降低了写日志到磁盘的数据量和频率. 通过合并sql, 能减少sql的解析次数, 减少网络传输的IO.

#### 		2.手动使用事务

​		mysql默认是autocommit的, 每次insert都会进行一次commit, 为了减少创建事务的消耗, 可以手动使用事务

### 	查询语句优化:

#### 		1:

# Mybatis

## 1.接口返回数据怎么做分页

# Socket

## 1.原生Socket实现

# Redis

1.原生Redis实现

2.SpringBoot整合Redis



# RabbitMQ

1.原生RabbitMQ实现

生产者:

消费者:







