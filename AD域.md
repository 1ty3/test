# AD域

因为一·开始：cannot find a valid baseurl for repo:base/7/x86_64

是因为centos不维护了

bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)需要换源

win和centos需要同一个网段才能添加域

还有关闭防火墙

必须要有网络

开始centos添加域过程

在 Windows AD 域环境准备好后，登录要加入域的 CentOS 系统,安装加域须要的安装包。

```shell
[root@host01 ~]# yum install realmd oddjob oddjob-mkhomedir sssd adcli openldap-clients policycoreutils-python samba-common samba-common-tools krb5-workstation

```





1.修改`/etc/resolv.conf`，添加域名和对应ip

```shell
vim /etc/resolv.conf
search guangdong.com
nameserver 192.168.100.128
```

2.修改`/etc/hosts`，添加ip及对应hostname

```shell
vim /etc/hosts
192.168.100.128 guangdong.com
```

3.编辑/etc/hosts

```
 vim  /etc/hosts
192.168.100.128 my guangdong.com
```

4.配置网卡DNS解析，解析地址为AD域服务器IP，如需其它DNS，可放在AD域服务器IP之后

```shell
vim /etc/sysconfig/network-scripts/ifcfg-enp0s3
...
DNS1=192.168.111.137

#重启网卡，使配置生效
systemctl restart network
```

5.检测域是否可达

```shell
ping guangdong.com
nslookup guangdong.com
```

6.设置可发现域

```shell
realm discover pbstest.com
```

7.加入域

```shell
# -v参数为可视化命令执行情况，带此参数可以看到加入域是否成功，如果不带则执行完直接返回到bash界面
# -U为user参数，后面需要带ad域里有管理权限的账号，可以是administrator等，后接域名即可
realm join  pbstest.com
```

8.验证是否加入域

```bash
realm list
```

9.验证是否存在id域用户

```
id xx@guangdong.com
cat /etc/shadow
```

10.登录是否存在id域用户

```shell
su - xx@guangdong.com
```

11.启用 sssd 验证。

```shell
[root@host01 ~]# authconfig --enablesssd --enablesssdauth --enablemkhomedir --update
[root@host01 ~]# systemctl start sssd
```



访问windowsserver 共享目录

## 使用samba-client 访问windows共享目录

 

```
## 列出所有的共享目录
#smbclient -L 10.203.105.145 -U administrator@xxx

## 访问共享目录  ReleasePackage 访问ip地址下的目录
#smbclient //10.203.105.145/ReleasePackage -U administrator@xxx 

##centos需要挂载win的话，需要centos挂载目录下
sudo mkdir /mnt/centosad
sudo mount -t cifs //192.168.100.128/centosad /mnt/centosad -o username="xiaolin",password="root123"

##挂载成功后，你可以通过以下命令查看挂载情况：
ls /mnt/centosad
```