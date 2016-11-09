# PPTP

install pptp on ubuntu 14.04 （openVZ or KVM）#在Ubuntu上安装PPTP VPN

##prepare #必要的工具及系统
putty 
ubuntu 14.04

##No.1 
putty configuration #软件配置
	enter  your username and password to log in ubuntu #输入用户名和密码连接系统

##No.2
configuration #开始配置
 #单击鼠标右键可以将剪切板内容复制进putty中
apt-get -y update
apt-get install pptpd   
apt-get install iptables

sudo cat > /etc/pptpd.conf << END
option /etc/ppp/pptpd-options
logwtmp
localip 192.168.0.1
remoteip 192.168.0.10-100
END	#配置网关和分配地址池

vim /etc/ppp/pptpd-options
	ms-dns 8.8.8.8
	ms-dns 8.8.4.4

cat >> /etc/sysctl.conf << END
net.ipv4.ip_forward = 1
END 	#vim /etc/sysctl.conf

sysctl -p #使修改生效

sudo iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
 #MASQUERADE伪装的意思，veneto公网接口可以通过ifconfig命令查看

sudo iptables-save > /etc/iptables.rules
 #保存IP转发规则

vim /etc/ppp/chap-secrets
 #添加pptp登录用户名和密码

/etc/init.d/pptpd restart
netstat -lntp
 #重启服务
