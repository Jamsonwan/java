# Linux

## LInux常用命令

* ls/ll
* cd
* mkdir
* rm -rf
* cp
* mv
* ps -ef | grep xxx
* kill 
* free -m
* tar -xvf xxx.tar

## 查看进程

* ps -ef | grep xxx
* ps -aux | grep xxx(-aux 显示所有状态)

## 杀死进程

* kill -9 pid (pid用查看进程的方式查找)

* 信号编号（-9 SIGKILL、-2 SIGINT， -15 SIGTERM）
  * SIGKILL： 强制终止
  * SIGINT：中断（先保存相关数据，然后在退出，control+c）
  * SIGTERM: 操作系统告诉应用程序应该主动关闭

## 启动服务

* 进入到应用的bin目录下
* 启动 ./startup.sh
* 关闭 ./shutdown.sh

## 查看日志

* cat
* tail
* vi

## 查看端口

* netstat -lnp（所有）
* netstat -anp | grep 端口号（查看某个端口是否被占用)

## 查找文件

* find 指定目录 -name xxx

## 查看Linux系统有几个cpu，以及每个cpu的核心数

* cat /proc/cpuinfo |grep -c 'pysical id' (cpu数)
* cat /proc/cpuinfo | grep -c 'processor'(核心数)

## 查看系统负载的命令

* w
* uptime

* 其中的load average即系统负载，三个数字分别表示一分钟、五分钟、十五分钟内系统的平均负载（平均任务数）

## vmstat

 * （Virtual Memory Statistics）对操作系统的虚拟内存、进程、CPU活动进行监控，是对系统的整体情况进行统计分析，
 * r 正在执行的任务数，b阻塞的任务数
 * i -- input 进入内存 o --从内存出去 s --swap 交换分区 b --block 块设备、磁盘

## 修改IP，关闭并重启

* vi /etc/sysconfig/network-scripts/ifcft-eth0
* 关闭 ifdown eth0  开启 ifup eth0
* 重启 service network restart

## 公司网站访问速度很慢很慢，怎么办

* **利用w命令或者uptime命令查看系统的负载情况，如果负载很高，利用top命令查看cpu，MEM等情况，要么是cpu繁忙、要么是内存不够。若都正常，可以用sar命令分析网卡流量，分析是不是受到攻击。一旦分析出了问题，就采取相应的措施，如决定要不要杀死一些进程，或者禁止一些访问等。**

