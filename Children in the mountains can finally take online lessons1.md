###                                                     # CentOS 7安装配置Shadowsocks客户端

#### 步骤1:预备工作：安装Python3.7版本:

首先安装编译环境

```mark
sudo yum -y groupinstall "Development tools"
```

再安装依赖包，以下罗列依赖包缺一不可。

```mark
sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel
```

然后获取 Python3 的安装包（本例使用 Python 3.7.3

```markd
wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz
```

解压安装包

```mark
tar -zxvf Python-3.7.3.tgz
```

切换到安装文件目录

```mark
cd Python-3.7.3
```

配置 Python 3.7.3 的安装目录

```mark
./configure --prefix=/usr/local/bin/python3
```

创建 Python3 软链接

```mark
ln -s /usr/local/bin/python3/bin/python3 /usr/bin/python3
```

创建 pip3 软链接

```mark
ln -s /usr/local/bin/python3/bin/pip3 /usr/bin/pip3
```

最后在命令行中输入 python3，能够进入 python3 终端即成功安装 

```mark
python3
Python 3.7.3 (default, May 15 2019, 17:07:20) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-36)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

#### 步骤2：安装vim

vim编辑器需要安装三个包`：`

```mark
vim-enhanced-7.0.109-7.el5
vim-minimal-7.0.109-7.el5
vim-common-7.0.109-7.el5
```

查看一下你本机已经存在的包，确认一下你的VIM是否已经安装：输入

```mark
rpm -qa|grep vim
```

这个命令，如何vim已经正确安装，则会显示上面三个包的名称

如果缺少了其中某个，比如说： vim-enhanced这个包少了，执行：

```mark
yum -y install vim-enhanced
```

命令，它会自动下载安装

如果上面三个包一个都没有显示，则直接输入命令：

```mark
yum -y install vim*
```

#### 步骤3：安装pip

#### pip 是 Python 的包管理工具，这里我们用 pip 安装 shadowsocks。

如果新系统尚未安装工具pip，可通过以下三步来快速完成pip的安装

1.

```mark
yum -y install epel-release
```

2.

```mark
yum install python-pip
```

3.

```mark
pip install --upgrade pip
```

#### 步骤4：安装Shadowsocks客户端

- 安装epel扩展源

- ```markdown
  sudo yum -y install epel-release
  ```

  采用Python包管理工pip安装

  ```mark
  sudo yum -y install python-pip
  ```

  安装Shadowsocks客户端

  ```mark
  sudo pip install shadowsocks
  ```

  ### 步骤5：配置Shadowsocks客户端

- #### 注意 Shadowsocks的配置文件自己创建

- 新建配置文件

- ```mark
  sudo mkdir /etc/shadowsocks
  ```

  ```mark
  sudo vi /etc/shadowsocks/shadowsocks.json
  ```

  添加配置信息

  # 千万注意，不能直接使用机场自带的JSON，必须按照这格式来写,自己对照改就是，不然无法启动

  ```mark
  {
  "server":"1.1.1.1",
  "server_port":1035,
  "local_address": "127.0.0.1",
  "local_port":1080,
  "password":"password",
  "timeout":300,
  "method":"aes-256-cfb",
  "fast_open": false,
  "workers": 1
  }
  ```

  fast_open：true或false。开启fast_open以降低延迟，但要求Linux内核在3.7+。开启方法

  ```mark
  echo 3 > /proc/sys/net/ipv4/tcp_fastopen
  ```

  ### 已经可以启动shadowsocks了，但我太菜了，还不会写自动启动脚本，我再研究研究。。。

  ```mark
  /usr/bin/sslocal -c /etc/shadowsocks/shadowsocks.json
  ```

  启动Shadowsocks客户端

  1.

  ```mark
  systemctl enable shadowsocks.service
  ```

  2.

  ```markd
  systemctl start shadowsocks.service
  ```

  3.

  ```mark
  systemctl status shadowsocks.service
  ```

  

  ### 另开一个终端或者后台运行!!!

  若Shadowsock客户端已正常运行，则结果如下：

  ```markd
  {
  "origin": "x.x.x.x" #你的Shadowsock服务器IP
  }
  ```

  ## 步骤5:安装配置Privoxy

  #### Shadowsocks是一个 socket5 服务，我们需要使用 Privoxy 把流量转到 http／https 上。

  

  安装Privoxy

  ```mark
  sudo yum -y install privoxy
  ```

  启动Privoxy

  1.

  ```mark
  systemctl enable privoxy
  ```

  2.

  ```mark
  systemctl start privoxy
  ```

  3.

  ```mark
  systemctl status privoxy
  ```

  ### 步骤6：配置Privoxy

  - 配置Privoxy
    ① 修改配置文件/etc/privoxy/config

  - ```markd
    sudo vi /etc/privoxy/config
    ```

    #### 确保如下内容没有被注释掉,第一个监听口估计都是一样的，第二个可能个人不一样，不用改，还是确保不被注释就行了，大佬说listen-address这一栏，如果你想让其他设备连接这个机器，ip写成0.0.0.0，我还没试过，以后试试

  - 设置http/https代理
    ① 修改配置文件/etc/profile

  - ```mark
    sudo vi /etc/profile
    ```

    在文本末尾添加一下信息

    ```mark
    export http_proxy=http://127.0.0.1:8118
    export https_proxy=http://127.0.0.1:8118
    ```

    启动privoxy

    ```mark
    source /etc/profile
    ```

    - 验证是否可用

    - ```mark
      curl www.google.com
      ```

      ### 备注：记得关闭防火墙

      查看防火墙状态

      ```mark
      firewall-cmd --state
      ```

      停止firewall

      ```mark
      systemctl stop firewalld.service
      ```

      禁止firewall开机启动

      ```mark
      systemctl disable firewalld.service
      ```

      

    - 

  - 

- 