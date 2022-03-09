# 面板相关代码

## 升级系统
### centos
```
yum update
```
### debian/ubuntu
```
apt update
```
### 提示/boot/grub/grub.conf 找不到
```
yum install -y grub
grub-mkconfig -o /boot/grub/grub.conf
```
### 提示/boot/grub2/grub.cfg 找不到
```
yum install -y grub2
grub2-mkconfig -o /boot/grub2/grub.cfg
```
## 开启VPS第三方ssh登录
### centos
```
sed -i 's/PermitRootLogin no/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
```
### debian/ubuntu
```
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
```

## 关闭防火墙
```
rm -f /etc/iptables/rules.v4
rm -f /etc/iptables/rules.v6
```
