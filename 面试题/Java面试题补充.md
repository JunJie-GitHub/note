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

组合模式: 项目需要树形结构存公司组织架构, 一个对象里存这个对象的List列表

装饰者模式: 装饰者模式就是定义很多装饰器, 继承于同一个接口, 调用一个方法的时候根据装饰器的不同实现不一样的代码逻辑, 动态地扩展实现类的功能. 我写过一个xml解析类, 传入一个HashMap, 存的是对象属性跟xml的对应关系, mybatis的resultMap标签的对象关系映射, key是属性名, value是xml的解析公式, 又传入类.class和xml字符串. 用的SAXReader获取xml元素值, 用反射判断属性类型并赋值. 可以认为这个hashmap就是一个装饰器, 闯入不同的hashmap就可以实现不一样的处理  



## 1.接口实现文件的下载上传

上传:

​	接口传入参数MutipartFile, 调用transfer(File file)方法可将文件写入磁盘

下载:

​	用response.getOutputStream()获取输出流, 调用write方法输出文件流

代码片段:

```java
    response.setCharacterEncoding("utf-8");
    response.setContentType("multipart/form-data");
    response.setHeader("Content-Disposition",
            "attachment;fileName=" + FileUtils.setFileDownloadHeader(request, downloadName));
```

```java
		fis = new FileInputStream(file);	//file是要下载的文件
        byte[] b = new byte[1024];
        int length;
        while ((length = fis.read(b)) > 0)
        {
            os.write(b, 0, length);	//os是HttpServletResponse response
        }
```

# SpringMVC



# SpringBoot



# 数据库

## 1.sql优化

**insert语句优化**:

1.合并多条语句为一条, 即insert into t values(a,b,c),(d,e,f)...

​		因为多条insert合并后的日志量减少了(MySQL的binlon和Innodb的事务日志), 降低了写日志到磁盘的数据量和频率. 通过合并sql, 能减少sql的解析次数, 减少网络传输的IO.

2.手动使用事务

​		mysql默认是autocommit的, 每次insert都会进行一次commit, 为了减少创建事务的消耗, 可以手动使用事务

**查询语句优化**:

不要用*查询, 不要加载不需要的列, 尽可能地让语句使用索引

1.where	and查询的顺序要跟联合索引的顺序一致

or查询语句会遍历所有数据, 可以用union分成两个小查询

order by排序的列顺序要跟索引的顺序一致, 否则排序会变成全表扫描

## 2.数据库7大范式

主要避免数据的重复性, 不是必须遵守的

第一范式:每个单元格都应该是单一值, 并且列不重复

第二范式:每个表都应该是单一功能的, 描述一个单一实体, 表中的属性只能描述这个表标的物

第三范式:表中的属性只依赖与主键, 不应该由其他属性推导来的

# Mybatis

## 1.接口返回数据怎么做分页

# Socket

## 1.原生Socket实现

服务端:

​	new一个ServerSocket(port), 

​	调用server.accept方法等待连接

​	socket.getInputStream获取输入流
​    

```java
public class SocketServer {
  public static void main(String[] args) throws Exception {
    // 监听指定的端口
    ServerSocket server = new ServerSocket(55533);
    // server将一直等待连接的到来
    System.out.println("server将一直等待连接的到来");
    Socket socket = server.accept();
    // 建立好连接后，从socket中获取输入流，并建立缓冲区进行读取
    InputStream inputStream = socket.getInputStream();
    byte[] bytes = new byte[1024];
    int len;
    StringBuilder sb = new StringBuilder();
    while ((len = inputStream.read(bytes)) != -1) {
      //注意指定编码格式，发送方和接收方一定要统一，建议使用UTF-8
      sb.append(new String(bytes, 0, len,"UTF-8"));
    }
    System.out.println("get message from client: " + sb);
    inputStream.close();
    socket.close();
    server.close();
  }
}
```
客户端:

new一个Socket(ip, port), 

执行getOutputStream获取输出流, 调用输出流的wriite(byte)方法发送数据

public class SocketClient {
    public static void main(String args[]) throws Exception {
        // 要连接的服务端IP地址和端口
        String host = "127.0.0.1";
        int port = 55533;
        // 与服务端建立连接
        Socket socket = new Socket(host, port);
        // 建立连接后获得输出流
        OutputStream outputStream = socket.getOutputStream();
        String message="你好  yiwangzhibujian";
        socket.getOutputStream().write(message.getBytes("UTF-8"));
        outputStream.close();
        socket.close();
    }
}

# Redis

1.原生Redis实现



2.SpringBoot整合Redis

 	引入redis的starter依赖

​	主配置文件配置redis信息	

​	用@Bean注册RedisTemplate, 设置连接工厂和序列化方式



3.Redis实现数据缓存



# RabbitMQ

1.原生RabbitMQ实现

生产者:	引入依赖, 配置application.yml, @EnableRabbit开启支持, 配置类用@Bean配置交换机 队列 绑定关系, 配置RabbitTemplate;或者@RabbitHandler+@RabbltListener配置消费者, 属性上就配了交换机队列, 路由key

消费者:	



# Netty

## 1.SpringBoot整合Netty



# SpringCloud

## 流量控制

### 1.Sentinel流控规则使用(入门)

1. 引入sentinel 核心依赖和注解支持依赖(原生依赖, 可以只用springcloud的starter)

   ```xml
   <!--        sentinel核心库-->
           <dependency>
               <groupId>com.alibaba.csp</groupId>
               <artifactId>sentinel-core</artifactId>
           </dependency>
   <!--        使用@SentinelResource注解需要引入这个依赖-->
           <dependency>
               <groupId>com.alibaba.csp</groupId>
               <artifactId>sentinel-annotation-aspectj</artifactId>
           </dependency>
   ```

2. 注册bean, SentinelResourceAspect

   ```java
   @Bean
    public SentinelResourceAspect sentinelResourceAspect(){
        return new SentinelResourceAspect();
    }
   ```

3. 定义资源, 设置流控降级后的处理方法

   ```java
   //配置规则
   @PostConstruct
   private static void initFlowRules() {
       //后续不会通过代码的方式设置规则
       List<FlowRule> rules = new ArrayList<>();
       FlowRule rule = new FlowRule();
       //针对资源进行限制
       rule.setResource(RESOURCE_NAME);
       rule.setGrade(RuleConstant.FLOW_GRADE_QPS);
       // Set limit QPS to 20.
       rule.setCount(1);
       rules.add(rule);
   }
   ```

   ```java
   /**
    * @SentinelResource 改善接口中资源定义和被流控降级后的处理方法
    * value:定义资源
    * blockHandler: 流控降级后的处理方法(默认要在同一个类里)
    * 不想在同一个类里用blockHandlerClass
    * fallback 接口出现异常, 交给指定的方法处理
    */
   @SentinelResource(value = USER_RESOURCE_NAME, blockHandler = "blockHandlerForGetUser")
   @GetMapping("/user")
   public User getUser(String id){
       return new User("1", "jerry", 10);
   }
   
   
   /**
    * 注意:
    * 1. 修饰符一定要public
    * 2. 返回值要和源方法保证一致, 包含源方法的参数, 流控后也能正确返回信息给用户
    * 3. 可以在参数后添加BlockException, 可以区分是什么规则的处理方法
    * 官网对该注解有详细的说明
    * @return
    */
   public User blockHandlerForGetUser(String id, BlockException ex){
       ex.printStackTrace();
       return new User(null, "流控!", null);
   }
   ```

### 2.Sentinel熔断规则

熔断后会过一段时间会进入半开状态, 半开状态第一次请求异常直接熔断, 不需要根据规则判断



### 3.Spring Cloud Alibaba整合Sentinel

1.引入依赖, sentinel版本需适配SpringCloud对应版本, [官方详细版本对应关系](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
    <version>2.2.1.RELEASE</version>
</dependency>
```

2.启动sentinel-dashboard-xxx.jar包, [github Releases](https://github.com/alibaba/Sentinel/releases)

​	默认端口8080, 用户名密码sentinel, 可自行设置, 启动bat脚本示例:

```cmd
java -Dserver.port=8088 -Dsentinel.dashboard.auth.username=honma -Dsentinel.dashboard.auth.password=honma -jar sentinel-dashboard-1.8.3.jar
pause
```

