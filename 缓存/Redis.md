# Redis

Remote Dictionary Server



## 安装

相关工具：

redis-benchmark：性能测试工具

redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲

redis-check-dump：修复有问题的dump.rdb文件

redis-cli：客户端，操作入口

redis-sentinel：redis集群使用

redis-sentinel：redis集群使用

redis-server：Redis服务器启动命令



注：有外网的服务器redis一定要设置密码，不然会被入侵挖矿

Linux用install安装redis默认路径

redis.config: /etc/redis/redis.conf

dump.rdb: /var/lib/redis/dump.rdb

#### **redis-config配置**

daemonize no	改为 daemonize yes（以守护进程后台运行）

protected-mode yes	改为 protected-mode no（保护模式关闭）

bind 127.0.0.1	默认只能本地访问，注释掉或改成本机ip地址

添加redis密码	requirepass foobared改成要设置的密码



## Redis数据类型

**String**：String是二进制安全的，可以存jpg图片或序列号对象，String类型的value最多可以是512M

**List**

**Hash**

**Set**

**ZSet**

其他：

​	GEO：地理空间

​	HyperLogLog：基数统计（不重复数据的的统计，例当天所有ip的访问量统计），HyperLogLog只需12KB内存就可以计算2^64不同元素的基数（不存储元素本身）

​	bitmap：位图

​	<img src="./Redis.assets/image-20230223151156808.png" alt="image-20230223151156808" style="zoom: 67%;" />	

​	bitfield：位域

​	Stream：流，用于消息队列，Redis支持发布订阅（pub/sub）实现消息队列，但没办法持久化，Stream提供了消息的持久化和主备复制功能，可以让客户端访问任意时刻的数据，记住每个客户端访问的位置



## 常用命令

命令查阅：https://redis.io/commands/

​	

## Redis实际应用经验
