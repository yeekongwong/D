# Redis-lua篇
标签（空格分隔）： Redis
---
## 在redis2.6之后可以调用lua脚本
## 1.lua是什么
> 是一个小巧的脚本语言，执行速度快，为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制功能。

## 2.lua的特性
> 1.可以减少网络开销;(和shell脚本差不多)本来是需要多个命令执行的，但可以通过lua脚本一次执行
2.原子操作
3.复用性;编写一个脚本多个地方使用
4.轻量级的脚本

## 3.lua安装
> 1.tar -zxvf ...
2.make linux(编译环境)
3.make install
4.输入lua 进入控制台

## 4.lua的基本语法
> 1.动态类型的语言 无需定义变量类型，自动根据值
2.变量：全局变量(a=1) 和 局部变量(local a=1)
3.逻辑表达式 + - / * %
4.关系运算符 a==b a~=b(不等于) a>=b 1=="1"(false,类型不会自动转换)
5.逻辑运算符 and、or、[not(a and b) 非]、a..b(拼接)、#a(计算a长度)
6.逻辑控制语句 
if  expression then
elseif expression then
else
end

## 5.lua与redis的结合
> **1.命令控制台**
./redis-cli 进去redis客户端
eval "return redis.call('set','key','value')"[脚本内容] 0[参数个数] [key的参数] [args的参数]
eval "return redis.call('set',KEYS[1],ARGV[1])" 1 hello world [必须大写]
``` ip_limit.lua 脚本文件名称
local num = redis.call("incr",KEYS[1]);
if num==1 then 
    redis.call("expire",KEYS[1],ARGV[1])
    return 1;
elseif tonumber(num)>= tonumber(ARGV[2]) then
    return 0;
else
    return 1;
end
./redis-cli --eval "ip_limit.lua" ip:limit:127.0.0.1 , 60000 10 [注意空格]
```
>**2.java代码**
String lua = "";
String sha = jedis.scriptLoad(lua);
List keys = new ArrayList<String>();
List argvs = ..
Object obj = jedis.evalsha(sha,keys,argvs);
**lua执行过程中，redis不可以执行其他命令，这是一个原子的过程**






