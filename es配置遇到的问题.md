>1.http:127.0.0.1:9200 正常访问，而外网ip拒绝连接；
解决办法：修改 config/elasticsearch.yml文件，增加network.host: 0.0.0.0


>2.max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
解决办法：切换root账户 修改/etc/sysctl.conf 增加vm.max_map_count=655360保存 执行sysctl -p

>3.max file descriptors [4096] for elasticsearch process likely too low, increase to at least [65536]
max number of threads [1024] for user [lwuser] likely too low, increase to at least [2048]
解决办法：修改 /etc/security/limits.conf 添加以下参数
/* soft nofile 65536
/* hard nofile 131072
/* soft nproc 2048
/* hard nproc 4096
使用以下命令查看是否修改成功
ulimit -Hn
ulimit -Sn
ulimit -Hu
ulimit -Su

>4.后台启动elasticsearch
nohup ./elasticsearch&
