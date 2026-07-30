# Windows Git配置（Github）

## Git下载与安装

访问Git官方网站:

```url
https://git-scm.com/
```

选择`install`，根据所使用的系统类型与是否安装于可移动介质，选择对应的安装程序下载，并运行程序。

## Git安装程序配置

按需选取所需安装的各种功能，设置Git的默认编辑器。这里选取`VScode`作为默认编辑器。选取`Git`初始化的分支名称，这里选择`Let Git decide`。选取路径环境，建议选取`Git from the command line and also from 3rd-party software`，以便此后可从`Powershell`运行`Git`命令。选择安装`Git`的方式，若选择`use bundled OpenSSH`，将会为`Git`安装独立的`OpenSSH`以进行连接，可单独进行配置密钥对，便于管理，但同时需注意配置`Git`时需配置其内置的`OpenSSH`；若选择`use external OpenSSH`，其将会使用环境变量中定义的SSH进行连接。选择`Https`传输后端（推荐默认）。选择行尾符号转换（推荐默认）。选择终端模拟器（推荐默认）。选取`pull`命令的默认行为（推荐默认）。选择凭证助手（推荐默认）。启用文件缓存`Enable file system caching`，启用符号链接`Enable symbolic links`。

## 验证Git安装

在`Powershell`中使用命令验证

```bash
git --version
```

应当出现版本信息

```bash
git version 2.54.0.windows.1
```

## 设置本地Git账号

在`Powershell`中使用命令

```bash
git config --global user.name "你希望使用的用户名，可以为Github ID"
git config --global user.email "你希望使用的邮箱，可以为Github账号"
```

测试是否配置完成

```bash
git config --global --list
```

应当出现

```
user.name=gardeniafall
user.email=hinatahysagm@gmail.com
```

## 配置Git与Github的链接

为了使得`Git`能够与`Github`进行连接，需要配置`OpenSSH`密钥对。

### 

**客户端配置**：这部分可以参考`SSH`目录下的密钥对生成与配置。需要注意，当配置使用独立`SSH`工具时，需要单独配置`Git`所使用的`SSH`配置，否则无法连接。若配置本机`SSH`，需注意文件名需为`id_rsa`。

**Github配置**：在`Github`中，需点击右上角头像进入`Settings`，在左侧`Access`栏选取`SSH and GPG keys`，在`SSH keys`处选择`New SSH Key`，填写标题，在下方填入公钥文件内的**所有内容**，然后点击`Add SSH key`即可。

## 测试Github连接

在`Powershell`中使用命令

```bash
ssh -T git@github.com
```

应当出现

```
Hi 你的GithubID! You've successfully authenticated, but GitHub does not provide shell access.
```

如果报错，可用调试工具

```bash
ssh -v -T git@github.com
```

进行调试。

也可以通过

~~~powershell
git config --global core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"
~~~

直接指定使用的`SSH`程序。

## 设置Git代理端口

若`Git`报错

```bash
fatal: unable to access 'https://github.com/gardeniafall/GPMD.git/': Failed to connect to github.com port 443 after 21113 ms: Could not connect to server
```

则很可能是代理设置问题，若有科学上网方法，可配置`Git`使用科学上网端口

```bash
git config --global http.proxy http://127.0.0.1:<你的代理端口>
```

若使用`Clash for Windows`，代理端口一般为`7890`。若使用`Clash Verge`，代理端口一般为`7897`。