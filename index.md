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
## X-ui面板
### X-ui面板部署
```
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)
```
### X-ui面板完全卸载
```
systemctl stop x-ui
systemctl disable x-ui
rm /usr/local/x-ui/ -rf
rm /etc/x-ui/ -rf
rm /etc/systemd/system/x-ui.service -f
systemctl daemon-reload
```
### X-ui面板其它操作
```
x-ui                  # 显示管理菜单 (功能更多)
x-ui start            # 启动 x-ui 面板
x-ui stop             # 停止 x-ui 面板
x-ui restart          # 重启 x-ui 面板
x-ui status           # 查看 x-ui 状态
x-ui enable           # 设置 x-ui 开机自启
x-ui disable          # 取消 x-ui 开机自启
x-ui log              # 查看 x-ui 日志
x-ui update           # 更新 x-ui 面板
x-ui install          # 安装 x-ui 面板
x-ui uninstall        # 卸载 x-ui 面板
```
### 忘记用户名和密码
使用以下命令重置用户名和密码，默认都为 admin
```
/usr/local/x-ui/x-ui resetuser
```
### 面板设置修改错误导致面板无法启动
使用以下命令重置所有面板设置，默认面板端口修改为 54321，其它的也会重置为默认值.注意，这个命令不会重置用户名和密码。
```
/usr/local/x-ui/x-ui resetconfig
```
