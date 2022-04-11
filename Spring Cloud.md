# Spring Cloud

## 服务注册中心

* 框架 Eureka（停更）
  * 服务治理：管理服务之间的依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。
  * Eureka Service 提供服务注册
    * 各个微服务节点通过配置启动后，会在EurekaServer中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点信息可以在界面中直观看到。
  * Eureka Client 通过注册中心进行访问
    * Java客户端，用于简化Eureka Server的交互，内置的、使用轮询（round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳（默认周期30s）。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除（默认90s）。
* Zookeeper
* Consul
* Dubbo

