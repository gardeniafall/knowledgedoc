# 公网服务器SSH安全配置

## SSH配置密钥登陆

1.**创建密钥对**

若已经拥有密钥对，直接分发公钥即可，**无需生成**！

服务端或客户端均可配置，只要成对即可。

~~~bash
#进入 SSH 目录
cd ~/.ssh/
#执行命令创建密钥对文件
ssh-keygen -t rsa
~~~

过程中是一些基础设置，包括密码设置。一般直接全部回车即可。生成后查看下是否生成成功，其中公钥为`id_rsa.pub`,私钥为`id_rsa`。

2.**客户端配置**

客户端访问时，需要持有私钥。在windows系统下，将私钥文件拷贝至C:\Users\\.ssh\目录下。可用终端进行访问：

~~~~~~bash
#windows SSH 连接
ssh username@address
~~~~~~

3.**服务端配置**

查看其中是否有 `authorized_keys`文件，若没有应当运行

~~~~~~bash
#生成密钥文件
touch authorized_keys
~~~~~~

将 `id_rsa.pub` 文件的内容输出到 `authorized_keys`

~~~~bash
#写入公钥
cat id_rsa.pub >> authorized_keys 
~~~~

至此，配置密钥完成。需重启SSH服务：

~~~bash
#重启SSH服务
sudo systemctl restart ssh
#查看SSH状态
sudo systemctl status ssh
~~~

## SSH配置可用登陆方式

当将服务器公开至公网时，往往需要禁止密码登录来保证安全。此时需要配置登录方式。进入SSH配置文件目录：

~~~bash
#进入SSH配置目录
cd /etc/ssh
#配置SSH服务端
nano sshd_config
~~~

配置如下内容：

~~~bash
#密码登录方式
PasswordAuthentication no
#密钥登录方式
PubkeyAuthentication yes
~~~

配置完成后，重启ssh服务：

~~~bash
#重启SSH服务
sudo systemctl restart ssh
~~~

若仍能登录，进入`sshd_config.d`目录，修改以下文件：

~~~bash
#修改文件
nano 50-cloud-init.conf
#修改内容
PasswordAuthentication no
~~~

配置完成后，重启ssh服务：

~~~bash
#重启SSH服务
sudo systemctl restart ssh
~~~

