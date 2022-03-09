# 面板相关代码
## 前期准备
### 修改时间
#### 获取root权限
```
sudo -i
```
#### 查看服务器时间
```
date
```
#### 修改时区
```
sudo tzselect
```
选择 亚洲Asia;
继续选择 中国China;
最后选择 北京Beijing.
或者在Asia后直接选择 Singapore
#### 创建时区软链
```
sudo ln -sf /usr/share/zoneinfo/Asia/Singapore /etc/localtime
```
#### 重启
```
reboot
```
### 升级系统
#### centos
```
yum update
```
#### debian/ubuntu
```
apt update
```
#### 提示/boot/grub/grub.conf 找不到
```
apt install -y grub
grub-mkconfig -o /boot/grub/grub.conf
```
#### 提示/boot/grub2/grub.cfg 找不到
```
apt install -y grub2
grub2-mkconfig -o /boot/grub2/grub.cfg
```
### 执行bbr加速脚本
```
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
chmod +x tcp.sh
./tcp.sh
```
### 开启VPS第三方ssh登录
#### centos
```
sed -i 's/PermitRootLogin no/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
```
#### debian/ubuntu
```
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
```

### 关闭防火墙
```
rm -f /etc/iptables/rules.v4
rm -f /etc/iptables/rules.v6
```
### 修改密码
```
passwd
```
### 重启
```
reboot
```
