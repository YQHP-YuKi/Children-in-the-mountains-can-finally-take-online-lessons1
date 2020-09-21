## 1.VMware正确的关机

### 执行命令：

```markdown
init 0
```

## 2.桥接模式

### 虚拟机ip地址需要与主机在同一网段，如果需要联网，则网关于DNS需要与主机网卡一致

## 3.NAT（ 地址转换模式）

### 在 NAT模式下，虚拟主机需要借助虚拟 NAT设备和虚拟DHCP服务器，使得虚拟机可以联网，虚拟机和物理机共有一个IP地址。

### 注：虚拟机使用NAT模式时，Linux系统要配置成动态获取IP

## 4.Host-Only模式

### 讲虚拟机与外网隔开，使得虚拟机成为一个独立的系统，只与主机相互通讯，相当于NAT模式去除了虚拟NAT地址转换功能。

### 注：虚拟机使用Host-Only模式时，Linux系统要配置成动态获取IP

## ifconfig命令使用方法

### 注意：下面操作 使用root（动态修改）

```mark
ifconfig
```



### 作用：用来配置网络或显示当前网络接口的状态

### 第一行：up-->网卡开启状态

### 							RUNNING-->网线处理连接状态

### 							MULTICAST-->支持组播

### 							mtu1500-->(Maximum Transmission Unit)

### 第二行：该网卡 的IP地址，子网掩码，广播地址

### 第三行：IPV6的配置信息

### 第四行：网卡的MAC地址

### 				  ether表示连接类型 为以太网

### 				  txqueuelen 1000-->>传输队列的长度

### 第五六行：网卡接收数据包的统计信息和接收错误的统计信息

### 第七八行：网卡发送数据包的统计信息和发送错误的统计信息

## 方法1：临时修改网卡IP地址

### ifconfig+网卡名称+IP地址 		------直接修改网卡的IP地址，重启失效

### 列如：

```markd
ifconfig ens33 192.168.1.110 netmask 255.255.255.0
```

### 说明：修改后当前终端会终端，需要重新使用新的IP地址进行连接

### CentOS7的网卡重启方法

```mark
systemctl restart network
```

### CentOS6的网卡重启方法

```mark
service network restart
```

### 	方法2：添加多个临时IP地址

### ifconfig 网卡名称0:第一个IP地址（netmask子网掩码）	---增加一个IP

### ifconfig网卡名称1:第二个IP地址（netmask子网掩码）	---增加一个IP

### 列如

```mark
ifconfig ens33:0 192.168.1.110 netmask 255.255.255.0
```

