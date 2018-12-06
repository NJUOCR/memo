一共五条命令，以及一些注意事项

 

Docker是使用iptables的NAT来管理端口映射的，所以实际是通过编辑/etc/sysconfig/iptables文件编辑端口映射规则来管理端口映射的。

使用一下三条命令来添加规则：

iptables -t nat -A POSTROUTING -s 172.17.0.2/32 -d 172.17.0.2/32 -p tcp -m tcp
--dport 27017 -j MASQUERADE

iptables -t nat -A DOCKER ! -i docker0 -ptcp -m tcp --dport 27017 -j DNAT --to-destination 172.17.0.2:27017

iptables -t filter -A DOCKER -d172.17.0.2/32 ! -i docker0 -o docker0 -p tcp -m tcp --dport 27017 -j ACCEPT

 

此处是将容器的27017映射到主机的27017，dport为宿主机端口，sport为容器端口。第一条命令应该是将宿主机的端口加入允许外部访问的范围，第二条是完成映射，第三条则是允许包过滤

 

此外，172.17.0.2为容器ip，可通过命令

docker inspect nifty_sammet |grep IPAddress

查看容器IP；

 

查看端口是否已经被占用，可以使用ss –lnp|grep 27017(如上，替换掉27017)

 

在添加完上面三条规则后，存储规则至/etc/sysconfig/iptables,否则无法生效

iptables-save > /etc/sysconfig/iptables

 

可以通过cat /etc/sysconfig/iptables确认是否更改成功

 

然后，docker stop [containername]

 

然后，重启iptables: systemctl restart iptables

 

再启动容器：docker start [容器名]

 

启动容器失败报错iptables failed:balabala，可能需要重启docker，然后docker start ‘docker ps –a –q’，（当然这是博客上看到的，我虽然遇到了，但是重启了iptables就好了）