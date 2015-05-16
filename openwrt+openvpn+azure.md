# OpenWrt + OpenVPN + Azure = 会翻墙的路由器
作为一个挨踢人，翻墙是基本技能，因为有太多好的技术资料在墙外了。当然，翻墙有很多方法，你喜欢用那种都可以。我原本一直在 PC 上通过 SSH 打隧道，有兴趣的话请参考我之前写的 双 Azure 虚机翻墙，看 YouTube 的 720P 视频绝对没问题。接下去的问题是，家里的那些手机们和其它移动端呢？难道要在每个端上都做配置吗？何不直接把家里的路由器翻出墙，这样家里所有的端都可以享受福利了。
![architecture](https://raw.githubusercontent.com/martincai/blogs/master/resources/openwrt-infra.png)

## 需要的资源
* 一台墙外的主机。我选择用 Azure 虚机，系统是 Ubuntu 14.04 LTS，选择最小配置的就可以。数据中心选 East Asia，在香港，离我们最近，所以速度会比较好。
* 一台 OpenWrt 支持的**[路由器](http://wiki.openwrt.org/zh-cn/toh/start)**。需要把路由器的 firmware 刷成 OpenWrt。如果你家现在用的路由器不在这个被支持的列表中，那就得去买个被支持的了。我用的是 TP-Link TL-WR740N v4 硬件版本，从淘宝上买的，连运费一共50元左右。你可以要求淘宝卖家帮你把路由器刷好，超级方便。

## 安装 OpenVPN 服务器
在 Azure 上创建虚机后，配置两个 Endpoints： TCP 22，UDP 1194。
![azure-endpoints](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-endpoints.png)

SSH 到虚机上，安装 OpenVPN 和 Easy RSA。
```bash
sudo apt-get update
sudo apt-get install openvpn easy-rsa
```
接着配置 Easy RSA，创建证书和密钥。
```bash
sudo mkdir easy-rsa
cd easy-rsa
sudo make-cadir my_ca
sudo bash
cd my_ca
vim vars
```
编辑 vars 里面的内容，确保以下的都不为空。
```bash
# These are the default values for fields
# which will be placed in the certificate.
# Don't leave any of these fields blank.
export KEY_COUNTRY="CA"
export KEY_PROVINCE="ON"
export KEY_CITY="Toronto"
export KEY_ORG="MyOrg"
export KEY_EMAIL="admin@company.com"
export KEY_OU="MyOrgUnit"

# X509 Subject Field
export KEY_NAME="server"
```





```bash
```
