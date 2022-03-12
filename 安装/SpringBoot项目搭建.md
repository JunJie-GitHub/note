1. ###### 新建project

   引入依赖jdbc, postgres驱动(根据使用的数据库, mybatis的starter, web的starter(一般都要)

2. 配置主配置文件

   1. 配置数据库连接池, 数据库驱动, 四大属性

![image-20220304180102041](SpringBoot%E9%A1%B9%E7%9B%AE%E6%90%AD%E5%BB%BA.assets/image-20220304180102041.png)

​		2.配置mybatis, mapper的xml路径, 开启驼峰式命名

​		![image-20220304180220943](SpringBoot%E9%A1%B9%E7%9B%AE%E6%90%AD%E5%BB%BA.assets/image-20220304180220943.png)

3. 启动类(或mybatis配置问题)添加mapper扫描路径

![image-20220304180402701](SpringBoot%E9%A1%B9%E7%9B%AE%E6%90%AD%E5%BB%BA.assets/image-20220304180402701.png)

4.写domain, mapper, service, controller测试启动