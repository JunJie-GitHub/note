1. ###### 新建project

   引入依赖jdbc, postgres驱动(根据使用的数据库, mybatis的starter, web的starter(一般都要)

2. 配置主配置文件

   1. 配置数据库连接池, 数据库驱动, 四大属性

![image-20220304180102041](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\image-20220304180102041.png)

​		2.配置mybatis, mapper的xml路径, 开启驼峰式命名

​		![image-20220304180220943](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\image-20220304180220943.png)

3. 启动类(或mybatis配置问题)添加mapper扫描路径

![image-20220304180402701](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\image-20220304180402701.png)

4.写domain, mapper, service, controller测试启动