# Nginx-进阶篇

标签（空格分隔）： Nginx

---

## 1.rewrite
> + 实现url的重写以及重定向
+ 语法：
    + if空格 (条件){设定条件进行重写}
    + return 停止处理并返回指定状态码给客户端
        if($reuqest_uri ~ *\.sh){return 403}

>   + set set 变量名 变量值
    + rewrite regex replacement [flag]{last/break/redirect/permant}
    
     location / {
        rewrite '^/images/([a-z]{3})/(.*)\.(png|jpg)$' /hzexiu?file=$2.$3;
        set $image_file $2;
        set $image_type $3;
    } 
    location /hzexiu {
        root html;
        try_files /$arg_file /image404/html;
        }
> 如上配置对于： /images/ttt/test.png 会重写到/hzexiu?file=test.png, 于是匹配到 location /hzexiu ;通过try_files获取存在的文件进行返回。最后由于文件不存在所以直接
返回404错误

## 2.缓存
>+ expires 时间 s|m|h|d
    + location ~ \.(png|jpg|js|css)${
        + root html/images;
        + expires 5m;
    + }

## 3.压缩gzip
> gzip on;
gzip_buffers 4 16k;
gzip_comp_level 7;
gzip_min_length 500;
gizp_types text/css text/xml ...


## 4.反向代理
> proxy_pass
location = /s{
proxy_pass http://www.baidu.com;
proxy_set_head X-Real_IP $remote_addr;
}
服务端获取的不是客户端真正ip，而已nginx的代理ip


## 5.负载均衡
> + upstream tomcatserver{
    //默认轮询
    server 127.0.0.1 weight=4;
    server 127.0.0.2 ；
}

> + 语法：server address [parameters]
    + 其中关键字server必选。
    + address也必选，可以是主机名、域名、ip或unixsocket，也可以指定端口号。
    + parameters是可选参数，可以是如下参数：
	    + down：表示当前server已停用
	    + backup：表示当前server是备用服务器，只有其它非backup后端服务器都挂掉了或者很忙才会分配到请求
	    +weight：表示当前server负载权重，权重越大被请求几率越大。默认是1
        + max_fails和fail_timeout一般会关联使用，如果某台server在fail_timeout时间内出现了max_fails次连接失败，那么Nginx会认为其已经挂掉了，从而在fail_timeout时间内不再去请求它，fail_timeout默认是10s，max_fails默认是1，即默认情况是只要发生错误就认为服务器挂掉了，如果将max_fails设置为0，则表示取消这项检查。

> + ups支持的调度算法
    + ip_hash  根据ip的hash值来做转发
    + 默认是轮询机制 权重 weight=x
    +  根据服务器的响应时间来分配请求
    + url_hash

