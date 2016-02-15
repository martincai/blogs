# Build your own VPN via dual Azure VM setup
There are plenty of ways to work around the Great Firewall of China. This blog describes how you can accomplish this task using two Azure virtual machines via SSH. The advantages are: (1) no need to install anything on the server; (2) with one VM in China and the other one outside, you directly connect to a local IP address. Note that the network between Azure data centers inside and outside China is usually very stable.
  
![azure-ssh-infra](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-ssh-infra.png)
  
Resources needed are: 1 Azure VM in China, 1 Azure VM outside China. Go with the lowest A0 VM spec is sufficient. I prefer Ubuntu 14.04 LTS.
  
**Step #1: create 2 Azure VMs (one in China, one outside China)**
  
![azure-create-vm](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-create-vm.png)
  
![azure-vm-config1](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-vm-config.png)
  
![azure-vm-config2](https://raw.githubusercontent.com/martincai/blogs/master/resources/azure-vm-config2.png)
  
Assume you have created two VMs successfully.
* China: azurevm-china.chinacloudapp.cn
* Global: azurevm-global.cloudapp.net
  
**Step #2: install SSH console on your PC client, PuTTY is pretty good, but I prefer [Git Bash](http://git-scm.com/downloads)**
  
**Step #3: create RSA key**
  
SSH to one of the VMs. It doesn't matter which one, then run the following command.
```bash
cd ~/.ssh
ssh-keygen –t rsa
```
![create-idrsa](https://raw.githubusercontent.com/martincai/blogs/master/resources/create-idrsa.png)
  
Copy id_rsa.pub (public key) to authorized_keys
```bash
cp id_rsa.pub authorized_keys
```
![copy-key](https://raw.githubusercontent.com/martincai/blogs/master/resources/copy-authkeys.png)
  
Use SCP to send id_rsa.pub (public key) to the other VM. Place the file at the same location ~/.ssh/authorized_keys
  
Use SCP to send id_rsa (private key) to your client PC.
  
**Step #4: configure SSH locally**
  
Create a new folder named .ssh under C:\Users\alias\ 
  
Create a new text file under C:\Users\alias\\.ssh using NotePad. Save it as config (note: there is no .txt extension).
  
Paste the following content inside the config file and save it.
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
Now, let's give it a try. Open Git Bash, cd C:\Users\alias\\.ssh and enter the following command:
```bash
ssh -D 127.0.0.1:7070 azureuser@proxy-global -i id_rsa
```
This will open port 7070 on your local PC as the tunnel. Of course, you may pick any unused port.
  
**Step #5: configure your Chrome browser with Proxy SwitchySharp**
  
![switchysharp](https://raw.githubusercontent.com/martincai/blogs/master/resources/switchysharp.png)
  
![chrome-switchysharp](https://raw.githubusercontent.com/martincai/blogs/master/resources/chrome-switchysharp.png)
  
This setup is pretty stable.
