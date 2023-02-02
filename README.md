# 小米CR660X解锁ssh，刷机openwrt/padavan固件


## 准备工作

一台笔记本，待刷机的路由器，这里使用的是cr6608移动定制版。

工具下载，<a href="https://github.com/kjfx/AX6000/releases/download/AX6000/AX6000-SSH.zip" target="_blank">点击下载>></a>

## 开始解锁

### 1.安装虚拟机

使用vmware将下载好的openwrt虚拟机安装好，

网络设置为桥接，勾选下面的选项。

### 2.启动虚拟机

启动虚拟机

### 3.连接虚拟机

OpenWrt 地址：192.168.5.1    用户名：root   密码：password

将电脑ip改成192.168.5.2，在浏览器中打开192.168.5.1 ，进入OP管理页面

### 4.修改OpenWrt的ip

进入管理页面后，选择网络，编辑右边lan口的配置，将ipv4地址修改为169.254.31.1，关闭下方的dhcp服务和下面的强制按钮，关闭ipv6，点击保存应用，若长时间卡在正在应用，刷新页面或者重启虚拟机，配置也是修改成功的。

### 5.上传xqsystem.lua到虚拟机

使用MobaXterm连接虚拟机中的openwrt，将xqsystem.lua文件拖到/usr/lib/lua/luci/controller/admin/这目录。

### 6.开启笔记本移动热点

开启 移动热点 ，并设置热点网络连接，去掉 Internet 协议版本 IPv4，

网络名称改成：OpenWrt，密码：12345678 ，频带：2.4GHz<br>

热点名称和密码要和下面的ssid/password一致，因为cr660x要连这个wifi

### 7.连接小米的wifi

连接小米的wifi，进入后台管理页面(地址在路由器背后)，移动定制版只需要输入密码(也在路由器背后写着)

，将地址栏中的stok后面的一串复制保存在一个地方。管理页面不要关闭。

### 8.编辑虚拟机的桥接网络

打开win10网络设配器管理，找到开的移动热点的设配器，点击属性，查看名称。

编辑 > 虚拟网络编辑器 > 更改设置，将openwrt虚拟机的网络桥接到刚查看的那个适配器上。

### 9.开始解锁

将下面的stok后面的xxx替换成刚保存的stok，ssid/password要与移动热点的名称密码相对应。

```
http://192.168.10.1/cgi-bin/luci/;stok=XXXXXX/api/xqsystem/extendwifi_connect_inited_router?ssid=OpenWrt&password=12345678&encryption=WPA2PSKenctype=CCMP&channel=1&band=2g&admin_username=root&admin_password=admin&admin_nonce=xxx
```

复制到浏览器地址栏，回车，等待片刻，如果显示code:0，则ssh已经解锁成功了。

### 10.刷入pb-boot.img

使用MobaXterm连接路由器，通过SN获取默认root账号的密码（SN码在路由器底部或192.168.31.1 能查看到），如果密码不对，可以尝试路由器背面的默认登录密码。可以使用以下网站获取。

```
https://www.oxygen7.cn/miwifi/
https://miwifi.dev/ssh
```

*如果以上计算密码网址打不开，请下载这个使用，<a href="https://github.com/kjfx/AX6000/releases/download/AX6000/SN.zip" target="_blank">点击下载>></a> <br>
<code>SN码格式：30212/F1ZD22032</code><br><br>

上载pb-boot.img到tmp文件夹中。

执行以下命令

```
mtd -r write /tmp/pb-boot.img Bootloader
```

### 11.刷入Openwrt/padavan

拔掉小米路由器电源，用网线将Lan口与笔记本的网卡相连，用牙签按住路由器后面的reset按钮，插电开机，等到电源灯长亮呼吸，在浏览器中输入地址(查看笔记本获取到的ipv4地址)，进入pbboot刷机页面，点击选择固件，选择你要刷的固件，点击确定，然后点恢复固件，等自动刷入完成后重启。进入新系统的管理页面配置网络。



## 常见问题：
**需要特别注意：<br>**
1、每次登录小米路由器后台 stok码 都会变动，请以最新登录的为准。<br>
2、解锁SSH成功之后有可能不会有任何提示，可以用putty登录查看是否解锁成功，显示“ARE U OK”就表示解锁成功。<br>
3、如果第一次没解锁成功，请重新登录小米路由器后台，再重新复制 stok码 ，再次解锁。<br><br>

4.提示"code fail",是因为热点wifi名称密码没有和下面的网站路径中的ssid/password对应。

**如果登录不了OpenWrt  192.168.5.1<br>**
1、重启一下OpenWrt，手动重启，或者输入命令：reboot<br>
2、首先确定OpenWrt的的IP是否改成了 169.254.31.1，改成了这个就要用这个IP登录<br>
3、将本地网络连接IP网段改为 OpenWrt IP 网段<br>
4、打开VMware：关闭正在运行虚拟机OpenWrt，编辑 > 虚拟网络编辑器 > 更改设置 > 还原默认设置<br>
5、在浏览器输入 OpenWrt IP 登录。<br><br>

