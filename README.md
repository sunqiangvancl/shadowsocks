# 背景
本文可以在云服务器上搭建影梭，并实现加速，来访问全球范围内的因特网。

内容分为以下几个部分 ：
1：购买、配置云服务器。 
2：搭建shadowsocks。 
3：安装锐速（一款服务器加速软件）。 
4：配置ss多用户以及流量限制。

# 购买云服务器
之前比较火的是AWS，首年免费，笔者也在AWS上搭建了SS，但是用着速度太慢了，而且流量限制太小（15GB）。 
最近发现了vultr这个云服务器商，试了下，看YouTube都1080P没问题。现在向大家介绍购买、配置的情况。 

官网链接：[vultr官网](https://www.vultr.com/?ref=7369964)
> 请点击链接进入，作者可获得推广收益，码文不易，请多支持。

## 注册登录
使用邮箱即可注册，会给邮箱发个激活链接，点击即可激活。

## 充值
可以使用支付宝，最小充值10刀，（最低配每月2.5刀，可以用四个月） 
注意，官网有使用PayPal支付，送100刀的活动，但是这100刀只能用来购买很贵的裸机（没有低于100刀/月的）所以没啥用。
现在在搞活动，推特分享可以直接赚3刀，可以无条件使用，这不又免费续了一个月。 


## 配置服务器

这里的配置，大家可以根据自己的需要来。我的配置如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181108224458347.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1Nlbmc=,size_16,color_FFFFFF,t_70)
节点：纽约 
操作系统：centos 7 x64 
配置：2.5刀/月（最低配） 
其他： Enable IPv6打钩
选择完成后，点击部署Deploy Now 
稍等几分钟后部署完成 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181108224518634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1Nlbmc=,size_16,color_FFFFFF,t_70)
点击下面的运行实例，进入详情 
可以看到IP，以及用户名、密码 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181108224535661.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1Nlbmc=,size_16,color_FFFFFF,t_70)

# 搭建SS
使用SSH工具连接服务器，笔者使用的是xshell5。 
使用上面的ip、用户名及密码登录云服务器。

## 更新软件包
```yum update```
## 安装Python
```yum install python-setuptools```

## 安装pip
```yum install python-pip```
可能会出现没有找到包的情况。需要先安装扩展源EPEL，之后再安装pip即可。
```yum -y install epel-release```
## 安装SS
```pip install shadowsocks```
## 配置SS
### 创建配置文件和目录
```mkdir /etc/shadowsocks```
```vim /etc/shadowsocks/ss.json```

可能没有安装vim,需要先安装vim```yum install vim```
配置文件内容如下：
```
{
    "server":"0.0.0.0",
    "server_port":443,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"mypwd",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false,
    "workers": 1
}
```
### 属性说明：

server：服务端监听地址(IPv4或IPv6)
server_port：服务端端口，一般为443
local_address：本地监听地址，缺省为127.0.0.1
local_port：本地监听端口，一般为1080
password：用以加密的密匙
timeout：超时时间（秒）
method：加密方法，默认为aes-256-cfb，较安全，rc4-md5速度更快
fast_open：是否启用TCP-Fast-Open，true或者false
workers：worker数量

# 执行命令
启动：```ssserver -c /etc/shadowsocks/ss.json -d start ```
停止：```ssserver -c /etc/shadowsocks/ss.json -d stop ```
重启：```ssserver -c /etc/shadowsocks/ss.json -d restart ```
无配置文件启动：```ssserver -p 443 -k MyPass -m rc4-md5 -d start```
设置为开机启动：打开配置文件```vim /etc/rc.local加入sudo ssserver -c /etc/shadowsocks/ss.json -d start ```
启动SS后无效果，可能的原因是防火墙设置 
添加开放端口：```firewall-cmd --zone=public --add-port=443/tcp --permanent```
重庆防火墙：```systemctl restart firewalld.service```

使用客户端连接VPN
可以使用各个操作系统的支持SS客户端，都在GitHub上有维护说明。 
参见：https://github.com/shadowsocks。

安装锐速加速
锐速是一款强大的服务器加速软件。由于这个软件只针对特定的linux内核才能使用，所以需要修改服务器系统的内核。 
对于centos7，内核更换为： 3.10.0-229.1.2.el7.x86_64

```
rpm -ivh http://soft.91yun.org/ISO/Linux/CentOS/kernel/kernel-3.10.0-229.1.2.el7.x86_64.rpm --force
```

# 重启服务器

reboot
# 安装锐速加速
```
wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/serverspeeder/master/serverspeeder-all.sh && bash serverspeeder-all.sh
```

启动锐速
```service serverSpeeder start```

# 多用户配置
闲了在写…..
