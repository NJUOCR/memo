## docker 中不能启用NVIDA显卡驱动
```
> nvidia-smi
Failed to initialize NVML: Insufficient Permissions
```
可能是由于docker宿主机启用了`selinux`，检查方法:

```
> /usr/sbin/sestatus -v
```

**禁用**：<br />
1. `setenforce 0` 临时关闭（不用重启）
2. 修改`/etc/selinux/config`将`SELINUX=enforcing`改为`SELINUX=disabled`
