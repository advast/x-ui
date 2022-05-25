# 面板相关代码
## 前期准备
### 获取root权限
```
sudo -i
```
### 清空iptables
#### 清空现有规则（常规安装失败才尝试此步骤）
```
sudo iptables -F
# 或者这个命令清空所有 groups
iptables-save | awk '/^[*]/ { print $1 } 
                 /^:[A-Z]+ [^-]/ { print $1 " ACCEPT" ; }
                 /COMMIT/ { print $0; }' | iptables-restore
```
#### 添加下列规则
```
# add SSH port first
iptables -I INPUT -p tcp --dport 21 -j ACCEPT		# ftp
iptables -I INPUT -p tcp --dport 22 -j ACCEPT		# ssh
iptables -I INPUT -p tcp --dport 80 -j ACCEPT   	# http
iptables -I INPUT -p tcp --dport 1080 -j ACCEPT		# mysql
iptables -I INPUT -p tcp --dport 443 -j ACCEPT		# https
iptables -I INPUT -p tcp --dport 465 -j ACCEPT		# mail
iptables -I INPUT -p tcp --dport 587 -j ACCEPT		# mail
iptables -I INPUT -p tcp --dport 993 -j ACCEPT		# mail
iptables -I INPUT -p tcp --dport 3306 -j ACCEPT		# mysql
iptables -I INPUT -p tcp --dport 3389 -j ACCEPT		# remotedesktop
iptables -I INPUT -p tcp --dport 8000 -j ACCEPT		# ajenti
iptables -I INPUT -p tcp --dport 9000 -j ACCEPT		# panel
iptables -I INPUT -p tcp --dport 9527 -j ACCEPT		# bot
iptables -I INPUT -p tcp --dport 9996 -j ACCEPT		# spare
iptables -I INPUT -p tcp --dport 9997 -j ACCEPT		# vmess
iptables -I INPUT -p tcp --dport 9998 -j ACCEPT		# vless
iptables -I INPUT -p tcp --dport 9999 -j ACCEPT		# trojan
iptables -I INPUT -p tcp --dport 10000 -j ACCEPT	# x-ui

# Set default chain policies
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Accept on localhost
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# Allow established sessions to receive traffic
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```
#### 保存配置
```
iptables-save > /etc/iptables/rules.v4
```
#### 重启
```
reboot
```
### 修改时间
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

### 关闭防火墙（若iptables规则已清空，则不要执行此命令）
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
使用以下命令重置所有面板设置，默认面板端口修改为 54321，其它的也会重置为默认值。注意，这个命令不会重置用户名和密码。
```
/usr/local/x-ui/x-ui resetconfig
```
### Clsah打开[TUN模式](https://docs.cfw.lbyczf.com/contents/tun.html)需要在配置文件添加以下内容
```
dns:
  enable: true
  enhanced-mode: fake-ip
  nameserver:
    - 8.8.8.8 # 真实请求DNS，可多设置几个
    - 114.114.114.114
# interface-name: WLAN # 出口网卡名称，或者使用下方的自动检测
tun:
  enable: true
  stack: gvisor # 使用 system 需要 Clash Premium 2021.05.08 及更高版本
  dns-hijack:
    - 198.18.0.2:53 # 请勿更改
  auto-route: true
  auto-detect-interface: true # 自动检测出口网卡 
```
## Oracle Cloud 甲骨文云账号登入状态的账户角色管理
```
https://cloud.oracle.com/access-management/services/
```
## 测速脚本
```
bash <(curl -Lso- https://git.io/superbench)
```
## Netflix解锁检测
```
wget -O nf https://github.com/sjlleo/netflix-verify/releases/download/2.52/nf_2.52_linux_amd64 && chmod +x nf && clear && ./nf
```
