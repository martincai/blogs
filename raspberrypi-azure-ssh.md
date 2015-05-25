## 树莓派变身翻墙服务器
继之前介绍的 **[双 Azure 虚机翻墙](https://github.com/martincai/blogs/blob/master/azure-2vm-ssh.md)** 后，有了新的需求，我那没越狱的 iPhone 怎么也可以轻松翻墙呢？参考将家里的路由器升级成为 **[高富帅路由器](https://github.com/martincai/blogs/blob/master/openwrt%2Bopenvpn%2Bazure.md)**，但对于很多人这个方法还是太复杂。另外，现在越来越多人家里已经没有了台式机或笔记本PC，用 iPhone/iPad 基本什么都可以做了。那我就讲一下用 Raspberry Pi 2 (树莓派 2) 来作为 SOCKS5 代理服务器来翻墙。
  
![raspberrypi-infra](https://github.com/martincai/blogs/blob/master/resources/raspberrypi2-arch.png)
  
首先，你需要在墙外有台服务器。请参考 **[双 Azure 虚机翻墙](https://github.com/martincai/blogs/blob/master/azure-2vm-ssh.md)**，当然只配置一台在墙外的 Azure 虚机也可以，不一定要两台。请准备好证书文件。
  
我买的树莓派板子是 **[Raspberry Pi 2 Model B](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/)**，装的系统是 Raspbian，其实就是个迷你 Linux 体统。通过 SSH 连到树莓派上，将 id_rsa 证书文件考到 `~/.ssh/` 下面。在 `~/.ssh/` 下面创建一个 config 文件，内容如下。
```bash
Host proxy-china
  HostName azurevm-china.chinacloudapp.cn
  Port 22
  User azureuser
  IdentityFile id_rsa

Host proxy-global
  HostName azurevm-global.cloudapp.net
  Port 22
  User azureuser
  ProxyCommand ssh -q proxy-china nc %h %p
```

强烈建议使用 AutoSSH 这个工具。假设树莓派的 IP 地址是 192.168.1.100，本地端口是 8080。
```bash
sudo apt-get install autossh
cd ~/.ssh
autossh -M 20000 -f -N -D 192.168.1.100:8080 proxy-global
```

在树莓派上安装一个 web 服务器。
```bash
sudo apt-get install apache2
sudo service apache2 start
```

在 `/var/www/html/` 下面创建一个新文件 `sudo vi proxy.pac`，内容如下。
```bash
function FindProxyForURL(url, host)
{
  return “PROXY 192.168.1.100:8080″;
}
```

在 iPhone/iPad 上，配置无线连接。打开 HTTP 代理，选择自动，输入以下地址。
```bash
http://192.168.1.100/proxy.pac
```
  
![iphone-http-proxy](https://github.com/martincai/blogs/blob/master/resources/iphone-http-proxy.jpg)

恭喜你的 iPhone/iPad 翻墙成功！
