Memcached 是一个分布式内存共享的技术，通常放置在应用的前端，实现高速缓存。
通常的应用方案有memcached+MySQL,实现 MySQL 数据库的加速。
通过 memcached 和tomcat 结合，可以实现 session 共享，即会话通memcached保存在内存中，多台tomcat服务器同时共享会话信息。
当tomcat服务器down机了，因为session是共享的，所以另外一台 tomcat 服务器直接读取共享的 session 即可。
Tomcat 服务器的调度则由 nginx 或者lvs 等调度器完成。同时，为了保障 memcached 服务器的高可用 HA 功能，可以部署 2 台以上，实现分布式的存取调度。

Memcached 共享配置：
实现 tomcat+Memcached session共享的配置需要设计到JDK环境的构建、tomcat 应用服务器的部署、Memcached 会话保持配置和 Nginx 负载均衡配置。

环境和需求说明：
实现 tomcat+Memcached 会话保持的网络拓扑其中 Nginx 充当前端的负载均衡调度器，
负责将请求分发给后端的 tomcat 服务器，后端的应用由2台 tomcat 服务器承担，
同时两台tomcat 服务器承担Memcached 的角色，并且互为主备，保障业务的高可用性。
通过共享存储，保障数据的一致性。

主机的相关环境如下：
IP 地址           主机名      操作系统       应用服务
192.168.44.4      nginx4     RHEL7.0       Nginx
192.168.44.11     JTM11      RHEL7.0       JDK+Tomcat+Memcached
192.168.44.12     JTM12      RHEL7.0       JDK+Tomcat+Memcached
实现目标： 
1. 通过 nginx，实现反向代理和负载均衡，并具有会话保持功能；
2. 两台 tomcat 服务器，实现 Java 的应用；
3. memcached 实现分布式会话共享功能，并实现 HA 功能；
4. 任意一台 tomcat 服务器出现故障，保障应用的可用性；
5. 任意一台 memcached 服务器出现故障，保障用户不掉线。
