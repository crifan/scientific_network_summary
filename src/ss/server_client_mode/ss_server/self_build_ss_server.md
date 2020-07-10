# 自己搭建Shadowsocks服务器

除了前面提到的，购买第三方的ss的服务之外，如果自己有能力，有精力，喜欢折腾，或者被逼无奈（之前就遇到20+1大时所有vpn和ss都被和谐了，所以逼的自己建ss服务器），则可以自己去搭建ss服务器。

## 自己搭建ss服务器的优缺点

自己搭建ss的服务器：

* 优点：
  * 自己可以任意配置
  * 账号可以给多人使用
  * 流量一般不是问题
* 缺点：
  * 需要自己折腾
  * 自己需要了解基本的技术才能去搭建
  * 万一被和谐了，VPS服务器的IP可能被封
    * 影响到已有的网站（如果有的话）

## CentOS中搭建自己的ss服务器

此处以Linux的CentOS中为例，介绍如何搭建自己的ss服务器。

具体实现方式，有多种，但多数都是Python的shadowsocks相关的包或库，然后简单设置一下即可。

### 准备工作

#### 准备一台可以访问国外网站的VPS主机

在自己搭建ss服务器之前，首先要求你自己有一台可以访问国外网站的服务器，典型的是去（比如[Linode](https://www.linode.com)、[Vultr](https://www.vultr.com)、[DigitalOcean](https://www.digitalocean.com/)等）主机提供商购买一个VPS主机，这样可以远程通过SSH工具登录主机，然后再去安装ss服务。

比如我此处就是用自己之前从Linode买的VPS，接着在上面折腾，安装ss服务，变成提供了ss服务器。

#### 最好用Python 2的版本

为了兼容性，此处最好Python是2.x的版本，比如此处的是2.7.5：

```bash
[root@crifan ~]# python --version
Python 2.7.5
```

#### Python包管理工具：pip

如果没有python的包管理工具，比如`pip`，则最好先去安装：

```bash
yum -y install python-setuptools && easy_install pip && pip install --upgrade pip
```

### 方式1: 用Python的shadowsocks实现ss服务

#### 用pip去安装shadowsocks

```bash
pip install shadowsocks
```

#### 配置shadowsocks

去添加配置文件`/etc/shadowsocks.json`。

内容可以参考下面的`shadowsocks-libev`的配置文件`/etc/shadowsocks-libev/config.json`

#### 启用shadowsocks

启动shadowsocks：

```bash
ssserver -c /etc/shadowsocks.json -d start
```

停止shadowsocks：

```bash
ssserver -c /etc/shadowsocks.json -d stop
```

### 方式2: 用shadowsocks-libev实现ss服务

#### 安装shadowsocks-libev

```bash
cd /etc/yum.repos.d/
curl -O https://copr.fedorainfracloud.org/coprs/librehat/shadowsocks/repo/epel-7/librehat-shadowsocks-epel-7.repo
yum install -y shadowsocks-libev
```

#### 配置shadowsocks-libev

新建配置文件（如果不存在的话）：

`/etc/shadowsocks-libev/config.json`

然后配置对应的参数：

* `server`：当前自己的服务器的地址
  * 比如：`0.0.0.0`
* `server_port`：ss服务的端口
  * 一般要大于1024，小于65536
  * 可以随意取值，只要不和其他端口冲突即可
    * 比如：`21500`
* `password`：ss客户端使用ss时要使用的密码
  * 比如一个复杂但安全的，包含数字和大小写字母的密码：`Ci20s5a6y96F`
* `method`：加密方式
  * 以前常见的方式是：`aes-256-cfb`
  * 最新更加复杂但更安全的是：`chacha20-ietf-poly1305`
    * 但是注意需要ss客户端要支持该加密方式才能正常使用ss服务
* `timeout`：超时时间，单位：秒
  * 比如：`300`
* `mode`：ss服务的模式
  * 比如：`tcp_and_udp`，即支持tcp也支持udp

举例，比如我此处的设置是：

```json
{
  "server": "0.0.0.0",
  "server_port": 21500,
  "password": "Ci20s5a6y96F",
  "method": "aes-256-cfb",
  "timeout": 300,
  "mode": "tcp_and_udp"
}
```

#### 启动shadowsocks-libev

* 启动：`systemctl restart shadowsocks-libev`

想要以后每次重启（Linux的CentOS）服务器时都可以自动启动此ss服务，则可以去：

* 设置开机启动：`systemctl enable shadowsocks-libev`

其他相关命令：

* 查看状态：`systemctl status shadowsocks-libev`
* 查看日志：`journalctl -u shadowsocks-libev`
* 重启：`systemctl restart shadowsocks-libev`

#### 如何确认ss是否正在正常运行

有多种手段查看ss是否正常运行：

##### systemctl status shadowsocks-libev

比如用
`systemctl status shadowsocks-libev`
去查看状态是否正常：

```bash
[root@crifan yum.repos.d]# systemctl status shadowsocks-libev
[0m shadowsocks-libev.service – Shadowsocks-libev Default Server Service
   Loaded: loaded (/usr/lib/systemd/system/shadowsocks-libev.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2017-10-23 20:32:02 CST; 25s ago
     Docs: man:shadowsocks-libev(8)
Main PID: 20915 (ss-server)
   CGroup: /system.slice/shadowsocks-libev.service
           20915 /usr/bin/ss-server -c /etc/shadowsocks-libev/config.json -u
Oct 23 20:32:02 crifan.com systemd[1]: Started Shadowsocks-libev Default Server Service.
Oct 23 20:32:02 crifan.com systemd[1]: Starting Shadowsocks-libev Default Server Service…
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: UDP relay enabled
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: initializing ciphers… aes-256-cfb
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: tcp server listening at 0.0.0.0:21500
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: udp server listening at 0.0.0.0:21500
```

##### journalctl -u shadowsocks-libev

可以查看对应的log输出是否正常，比如：

```bash
[root@crifan yum.repos.d]# journalctl -u shadowsocks-libev
— Logs begin at Thu 2017-10-12 03:56:54 CST, end at Mon 2017-10-23 20:39:17 CST. —
Oct 23 20:32:02 crifan.com systemd[1]: Started Shadowsocks-libev Default Server Service.
Oct 23 20:32:02 crifan.com systemd[1]: Starting Shadowsocks-libev Default Server Service…
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: UDP relay enabled
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: initializing ciphers… aes-256-cfb
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: tcp server listening at 0.0.0.0:21500
Oct 23 20:32:02 crifan.com ss-server[20915]: 2017-10-23 20:32:02 INFO: udp server listening at 0.0.0.0:21500
```

##### netstat -autn

通过命令：`netstat -autn` 或：`netstat -lnp`，去查看ss的端口是否正常：

```bash
[root@crifan yum.repos.d]# netstat -autn
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:21500           0.0.0.0:*               LISTEN     
...
tcp        0      0 45.79.205.194:443       222.188.39.139:52004    ESTABLISHED
...
tcp        0      1 45.79.205.194:80        61.147.173.53:45254     LAST_ACK   
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 0.0.0.0:21500           0.0.0.0:*                          
```

其中的`21500`就是上面设置的ss的端口，对应着`tcp`和`udp`的服务，表示都在正常运行。

##### ps ax |grep ss-server

也可以用`ps ax |grep ss-server`去看看当前已经通过ss-server启用了哪些ss的服务

#### 用shadowsocks-libev实现多账号/多用户

上述创建的ss服务，只有一个（账号）密码，只能供一个人用，或者说，如果多个人使用，则使用的是同一个（账号）密码，显得很不方便和不安全。

如果想要多个用户每人有不同的账号（密码），则可以：用shadowsocks-libev实现多账号/多用户

新建另外一个配置文件，比如：

`/etc/shadowsocks-libev/config2.json`

内容和之前一致，只是端口号`server_port`和密码`password`改了一下即可：

```json
{
    "server": "0.0.0.0",
    "server_port": 21501,
    "password": "passowrd2",
    "method": "aes-256-cfb",
    "timeout": 300,
    "mode": "tcp_and_udp"
}
```

 > 注意: 端口号不要和系统中其他服务的端口号冲突了。

另外再去用：

```bash
setsid ss-server -c /etc/shadowsocks-libev/config2.json -u
```

启用新端口对应的ss服务。

## 说明+坑+注意事项

关于更多的折腾的细节可以参考：[【已解决】Linode的CentOS 7中搭建shadowsocks – 在路上](https://www.crifan.com/linode_centos_7_install_and_config_shadowsocks/)

此处把一些常见的坑/注意事项/说明解释，整理如下：

### Shadowsocks官网

#### 官网也有介绍如何安装ss服务端

其实Shadowsocks的官网中也有管理server端的介绍：[Shadowsocks - Servers](https://shadowsocks.org/en/download/servers.html)，也可以参考官网去安装服务端的ss。

#### 官网也有介绍如何ss的配置的

官网也有关于ss的配置信息的介绍的，包括：

* 基本的配置信息的介绍：[Shadowsocks - Quick Guide](https://shadowsocks.org/en/config/quick-guide.html)
  * 其中包含关于二维码的规则的介绍
* 更加高级的配置：[Shadowsocks - Advanced](https://shadowsocks.org/en/config/advanced.html)

### 建议设置一个复杂但安全的密码

为了使得自己的ss的相对更加安全，建议设置一个相对比较复杂的密码

比如可以使用第三方密码生成工具：[Generate Password](http://ucdok.com/project/generate_password.html)

去生成一个比较复杂的密码，就类似于：`ci20s5a6y96f`

### FirewallD is not running

在启用ss服务期间，在检查是否可能是由于防火墙的问题而导致ss服务没有正常运行时，可能会遇到：

`FirewallD is not running`

-> 其表示：防火墙并没有运行

-> 对应着（和我此处类似的Linode的）CentOS中的防火墙，默认是关闭的

-> 所以不是防火墙的问题。
