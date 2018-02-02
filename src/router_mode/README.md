# 路由器模式去使用Shadowsocks

如何通过路由器模式去利用Shadowsocks实现科学上网，其大致的步骤是：

* 也是先确保自己有了一个（VPS）服务器，其中装了对应版本的shadowsocks-libev的ss的客户端
* 然后再确保自己买的路由器支持刷OpenWRT固件
* 然后再里面装上合适的版本的OpenWrt的ss的客户端
  * [Releases · shadowsocks/openwrt-shadowsocks](https://github.com/shadowsocks/openwrt-shadowsocks/releases)
* 然后再进行后续的职能自动科学上网方面配置即可

具体的操作过程，可以参考别人的教程：

[OpenWrt智能、自动、透明翻墙路由器教程](https://softwaredownload.gitbooks.io/openwrt-fanqiang/)
