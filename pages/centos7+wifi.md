# CentOS 7 连接 WiFi 相关设置

> From [CSDN](https://blog.csdn.net/GOODter/article/details/141994579)

在此记录一下centos7如何使用命令行连接、修改WiFi设置。
使用 nmcli 相关命令

查看网络连接状态：

```bash
[root@a ~]# nmcli dev status
DEVICE  TYPE      STATE         CONNECTION 
wlp2s0  wifi      disconnected  --   
lo      loopback  unmanaged     --         
```

可以看到 WiFi 网络是未连接的状态。

接下来扫描周围可用的WiFi网络

```bash
[root@a ~]# nmcli d wifi list
IN-USE  SSID                          MODE   CHAN  RATE        SIGNAL  BARS  SECURITY  
        TP-LINK_****                  Infra  3     270 Mbit/s  100     ▂▄▆█  WPA2      
        CMCC-****                     Infra  10    130 Mbit/s  100     ▂▄▆█  WPA1 WPA2 
        *****-5G                      Infra  149   270 Mbit/s  100     ▂▄▆█  WPA1 WPA2 
        *******_5G                    Infra  44    270 Mbit/s  99      ▂▄▆█  WPA2      
        --                            Infra  44    270 Mbit/s  99      ▂▄▆█  WPA2      
        *******                       Infra  44    135 Mbit/s  44      ▂▄__  WPA2      
        FAST-****                     Infra  6     130 Mbit/s  39      ▂▄__  WPA2      
        TP-LINK_*******               Infra  6     130 Mbit/s  34      ▂▄__  WPA2      
        MI-****                       Infra  6     130 Mbit/s  29      ▂___  WPA2      
```

接下来连接WIFI网络使用命令：

```text
<https://xinyzhao.github.io/>
```

连接后让我们来检查一下连接状态：nmcli dev status

```bash
[root@a ~]# nmcli dev status
DEVICE  TYPE      STATE      CONNECTION 
wlp2s0  wifi      connected  TP-LINK_**********   
lo      loopback  unmanaged  --         
```

现在网络已成功连接，如果要切换网络就再次使用上面的WIFI网络连接命令。

从其他地方看到了删除已连接WIFI网络的命令，顺便记录一下：

```bash
[root@admin ~]# nmcli c del '要删除连接的WIFI名称'
```

END.
