docker容器动态添加端口映射

这里完成的容器端口和宿主机端口的映射。（如果仅仅是应用的话，直接把命令里面的IP，端口，桥接网络视情况替换就好）

首先查看容器IP：

    docker inspect 【容器名】|grep IPAddress



查看端口是否已经被占用(此处查看的是mongodb的默认端口27017)

    ss -lnp|grep 27017



docker的端口映射本质上是通过iptables完成的，所以添加端口实际是使用iptables命令来操作的，

IPTABLES 是与最新的 3.5 版本 Linux 内核集成的 IP 信息包过滤系统。如果 Linux 系统连接到因特网或 LAN、服务器或连接 LAN 和因特网的代理服务器， 则该系统有利于在 Linux 系统上更好地控制 IP 信息包过滤和防火墙配置。                                                                              ——百度百科

额，iptables在我理解就是做ip和端口过滤的防火墙，然后通过过滤表这种很方便的形式增删改查规则。过滤表的位置是/etc/sysconfig/iptables。基本查看一下文件然后参照之前创建容器时添加的容器映射，添加一下对应的规则就可以完成动态添加端口。



    iptables -t nat -A POSTROUTING -s 172.17.0.2/32 -d 172.17.0.2/32 -p tcp -m tcp --dport 27017 -j MASQUERADE

对通过指定端口对容器的访问进行源地址转换，因为是动态ip所以使用masquerade，一种特殊的SNAT。

-t选择的是过滤表，因为是做端口映射，所以这里选nat，-A就是append，在过滤表末尾添加规则。postoutting应该是指将宿主机的端口添加到允许外部访问的范围。这里的dport就是宿主机端口。-j则是采用的操作。



    iptables -t nat -A DOCKER ! -i docker0 -p tcp -m tcp --dport 27017 -j DNAT --to-destination 172.17.0.2:27017

指定网络为默认网卡docker0，在访问端口27017时，目标地址转换为172.17.0.2:27017。



    iptables -t filter -A DOCKER -d 172.17.0.2/32 ! -i docker0 -o docker0 -p tcp -m tcp --dport 27017 -j ACCEPT

在filter表中添加允许对172.17.0.2:27017的访问。



    iptables-save > /etc/sysconfig/iptables

保存添加的规则，否则无法生效。



最后暂停容器，重启iptables，启动容器，测试端口是否可以被访问。



中间遇到的问题：

启动容器失败报错iptables failed:balabala，可能需要重启docker，当然这是网上看到的，我虽然遇到了，但是重启了iptables就ok了。
