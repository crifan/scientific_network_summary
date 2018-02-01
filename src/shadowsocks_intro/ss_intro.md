# Shadowsocks简介

## Shadowsocks是什么

**Shadowsocks**是一套技术和相关工具的总称，常被简称为**ss**

> 而这套技术，至少包含这些大的部分：
>
> * Shadowsocks技术本身
>   * SOCKS5代理
>   * AEAD
>   * 等等
> * Shadowsocks的服务端
>   * Python的shadowsocks
>   * shadowsocks-server
>   * shadowsocks-libev
>   * libQtShadowsocks
>   * shadowsocks-go
>   * 等等
> * Shadowsocks的客户端
>   * Window
>   * Mac
>   * iOS
>   * Android
>   * 等等

Shadowsocks在技术上可以简单总结为：

一套基于**Socks5**代理方式的网络加密传输数据包的技术

= **用socks5技术加密的技术**

## 如何利用Shadowsocks实现科学上网

而把Shadowsocks技术用于科学上网，则：

* **服务器**：首先需要有个ss的服务器
  * 服务器本身自己能够访问国外的网络
  * 当然，同时该服务器上运行了ss的服务
  * 即：服务器+ss服务，则一般有两种方式：
    * 去买Shadowsocks第三方服务商提供的服务器
    * 或者是自己买了VPS服务器后自己搭建出来的Shadowsocks服务器
* **客户端**：然后需要对应客户端，才能用起来Shadowsocks的服务，才能科学上网
  * 不同的平台下，需要安装不同的客户端
    * 且要支持对应的ss服务器所设置的加密方式
      * 尤其是部分ss服务器采用最新的更复杂的加密方式，还要求客户端是最新版才支持，否则无法使用

之后就会详细介绍，如何购买ss的服务或自己搭建ss服务器，以及如何在Mac/Windows/iOS/Android等不同平台中使用ss实现科学上网。

## Shadowsocks技术的官网

Shadowsocks这套技术，有个自己官网：

[Shadowsocks - A secure socks5 proxy](http://shadowsocks.org/en/index.html)

http://shadowsocks.org/en/index.html

shadowsocks.org：

* 本身是：非盈利的
* 作用是：推广和宣传ss技术

另外，对应的ss技术的github主页是：[shadowsocks](https://github.com/shadowsocks)

> **[warning] 不要把ss技术官网和普通的提供ss服务的公司的官网搞混淆了**
>
> 请注意：不要把ss技术的官网，和其他提供ss服务的公司的网站，比如：
> * shadowsocks.com -> 现已改为：shadowsocks.la
> * shadowsocksvpn.com
>
> 搞混淆了。
