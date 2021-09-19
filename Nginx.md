.Nginx install

..安装依赖

​	yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel

..查看已经开放的端口

.firewall-cmd --list-all

..增加新开端口

..sudo firewall-cmd --add-port=8081/tcp --permanent

firewall-cmd --reload

.Nginx 常用命令

..启动：

​	./nginx

..关闭

   ./nginx -s stop

.. 重新加载

  ./nginx -s reload



.Nginx配置文件nginx.conf

..全局块

​	worker_processes 1; # 处理并发的数量

.. events 块（服务器与用户网络的连接）

 	worker_connections 1024; #最大连接数

.. http 块

​	...全局块

​		

​	..server 块

​		...反向代理:

​			loaction / {

​                     root html;

​					proxy_pass http://127.0.0.1:8080;

​					index index.html index.htm;

​				}

​		....不同的端口，走不同代理

​			server{

​		 	listen 9001;

​			sever_name localhost;

​			location ~ /edu/{

​					proxy_pass http://localhost:8081;	

​			}

​			location ~ /vod/{

​					proxy_pass http://localhost:8082;

​			}

​		}

.负载均衡(轮询、weight、ip_hash、fair响应时间)

... 在events 块和server模块之上

upstream myserver{

​	# ip_hash;

​	server 192.168.129:8058;

​	server 192.168.129:8081;

​	# fair;

}

sever{

​	....

​		loaction / {

​                    root html;

​					proxy_pass http://myserver;

​					index index.html index.htm;

​				}

}

.动静分离(动态tomcat)

loaction /www/ { # 静态资源

​                   root /data/;

​					index index.html index.htm;

}

loaction /image/{# 静态资源

​                   root /data/;

​					index index.html index.htm;

​					autoindex on;

}





