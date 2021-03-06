# Hystrix 熔断降级
## 配置：
	windows中配置host文件中的域名解析，在C:\Windows\System32\drivers\etc\HOSTS中添加如下配置<br>
	127.0.0.1 eureka7001.com<br>
	127.0.0.1 eureka7002.com<br>
	127.0.0.1 eureka7003.com<br>
## 数据库脚本：
```clouddb01```:
DROP TABLE IF EXISTS `dept`;<br>
CREATE TABLE `dept` (<br>
  `deptno` bigint(20) NOT NULL AUTO_INCREMENT,<br>
  `dname` varchar(60) DEFAULT NULL,<br>
  `db_source` varchar(60) DEFAULT NULL,<br>
  PRIMARY KEY (`deptno`)<br>
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;<br>
<br>

## 启动顺序：<br>
	1.首先启动eureka集群：microservicecloud-eureka-7001、microservicecloud-eureka-7002、microservicecloud-eureka-7003<br>
	2.然后启动带有熔断功能得服务提供者microservicecloud-provider-dept-8001
	3.最后启动服务消费者：microservicecloud-consumer-dept-feign<br>
	其中microservicecloud为所有服模块的父工程，microservicecloud-api是公共模块<br>
	其他微服务不用管<br>
	<br>
## 结果呈现：
	访问:<br>
    先访问数据库中有数据id：http://localhost/comsumer/dept/get/id   如http://localhost/comsumer/dept/get/1<br>
    显示数据正常，然后关闭microservicecloud-provider-dept-8001微服务，再访问http://localhost/comsumer/dept/get/1<br>
    出现提示信息："没有没有对应的信息,Consumer客户端提供的降级信息,此刻服务Provider已经关闭"<br>
    再访问数据库中没有数据id:http://localhost/comsumer/dept/get/100<br>
## 注意:（很重要熔断与降级不一样）
  Feign对Hystrix的支持默认是关闭的，如果想开启对Hystrix的支持，你不仅需要在application.properties中添加：<br>
  feign.hystrix.enabled=true<br>
  
	eureka服务注册中心：http://eureka7001.com:7001、http://eureka7002.com:7002、http://eureka7003.com:7003<br>
