# Nginx

标签（空格分隔）： Nginx

---

## 1.正向代理和反向代理
> + 正向代理：代理的对象是客户端(访问谷歌需要vpn代理服务器)
+ 反向代理： 代理的是服务端(负载均衡，多个服务器，客户端访问代理服务器)

## 2.常用web服务器介绍
> apache,nginx,tomcat,jetty

> + nginx
    - 支持5w并发连接数的响应
    - 内存消耗小
    - 成本低

## 3.安装与命令
>+ 安装 
    + 解压 tar -zxvf nginx.tar.gz
    + ./configure --prefix=指定安装的路径 默认是到/user/local/nginx
    + make & make install
+ 启动
    + cd /sbin ./nginx -c ../conf/nginx.conf
+ 停止
    + ./nginx -s quit
    + ./nginx -s stop
    + ./nginx -s reload 重新加载配置文件
    + kill QUIT/TERM(-9) 进程号 （信号方式）
+ 验证配置
    + ./nginx -t
## 4.配置
>+ 三个核心配置
    - main 
        - worker_processes 1; 工作进程数
        - error_log logs/error.log notice 日志输出
    - events 
        - worker_connections 1024;
    - http
        - log_format myformat .... 日志格式
        - access_log logs/access.log myformat;
        - server
            - listen 80; 监听端口
            - server_name www.hzexiu.com; 域名
            - location /  匹配规则-根据用户请求的uri
+ 虚拟主机设置
    - 基于端口
    - 基于域名
    - 基于ip
+ 日志配置
    - 日志切割 
        - mv access.log access.log.20190316
        - kill -USR1 Nginx 主进程号 让nginx重新生成一个新的access.log
+ location
    - location [~|=|^~|~*] /uri {  }
    - 匹配规则
        - 精准匹配 location =/hzexiu.html 优先级最高
        - 一般匹配 location /hzexiu.html  优先级高于正则匹配
            - 如果存在多个相同前缀的一般匹配，则会按照最长的来匹配
        - 正则匹配 


