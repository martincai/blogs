# 双 Azure 虚机翻墙
现在翻墙是每个挨踢人的基本技能。市面上翻墙软件和方法很多，今天就八一下用SSH配上双Azure虚机来翻。这样的好处是：（1）不需要在服务器端装任何东西 （2）双 Azure 虚机，一个在国内，一个在国外，你直接连的是国内 IP 地址，不会被墙而且速度快。Azure 国内和国外的数据中心之间的连接是相当稳定的。
  
![azure-ssh-infra](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-ssh-infra.png)
  
需要的资源：一台虚机在国内 Azure 上（中国北部或东部都可以），一台虚机在国外 Azure 上（East Asia 数据中心离我们最近）。虚机的配置最低配 A0 就可以，我一直用的是 Ubuntu 14.04 LTS。
  
**第一步：在 Azure 上创建虚机，分别在国内的和国外的 Azure 各建一台**
  
![azure-create-vm](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-create-vm.png)
  
![azure-vm-config1](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-vm-config.png)
  
![azure-vm-config2](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-vm-config2.png)
  
假设你已经建好了两台虚机。
* 国内： azurevm-china.chinacloudapp.cn
* 国外： azurevm-global.cloudapp.net
  
**第二步：安装 SSH 工具，PuTTY 挺好用，我更喜欢用 [Git Bash](http://git-scm.com/downloads)**
  
**第三步：创建 RSA key**
  
SSH 到其中一台虚机上，随便哪台都可以，然后跑以下命令。
```bash
cd ~/.ssh
ssh-keygen –t rsa
```
![create-idrsa](https://raw.githubusercontent.com/martincai/blogs/master/resources/create-idrsa.png)
  
将 id_rsa.pub (public key) 复制到 authorized_keys
```bash
cp id_rsa.pub authorized_keys
```
![copy-key](https://raw.githubusercontent.com/martincai/blogs/master/resources/copy-authkeys.png)
  
用 SCP 把 id_rsa.pub (public key) 传到另一台虚机上，然后在那里也将 public key 复制的到相同地方 ~/.ssh 下的 authorized_keys
  
用 SCP 把 id_rsa (private key) 传到本地PC上。
  
**第四步：配置本地SSH配置**
  
在你的 C:\Users\用户名\ 下建一个新的文件夹 .ssh
  
在 C:\Users\用户名\.ssh 下建一个新文件，用 Notepad 就可以，保存为 config （注意：没有 .txt 后缀）
  
将以下内容粘贴在 config 里面并保存
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
现在让我们试一下吧。打开 Git Bash，cd到 C:\Users\用户名\\.ssh 下面，执行以下命令：
```bash
ssh -D 127.0.0.1:7070 azureuser@proxy-global -i id_rsa
```
这样就在本地机器上打开了 7070 端口的隧道，当然你可以选择任何你喜欢的端口来用。
  
**第五步：配置浏览器，在 Chrome 里用 Proxy SwitchySharp**
  
![switchysharp](https://raw.githubusercontent.com/martincai/blogs/master/resources/switchysharp.png)
  
![chrome-switchysharp](https://raw.githubusercontent.com/martincai/blogs/master/resources/chrome-switchysharp.png)
