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



# SpringMVC

## 1.接口实现文件的下载上传





# Socket

## 1.原生Socket实现

# Redis

1.原生Redis实现

2.SpringBoot整合Redis



# RabbitMQ

1.原生RabbitMQ实现

生产者:

消费者:







