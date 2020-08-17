### ubuntu 网络配置

- 网卡up/down

  ```shell
  sudo ifconfig eth0 up/down
  ```

- 分配IP地址

  ```shell
  sudo dhclient eth0
  ```

  当系统reboot后，该网卡配置会失效，需重新配置

- netplan

  ubuntu从17.10版本后

  ```shell
  sudo vim /etc/netplan/*.yaml
  #若无此yaml文件，则用netplan命令生成
  sudo netplan generate
  
  *.yaml:
  network:
  	version: 2
  	renderer: networked/NetworkManager #可缺省
  	ethernets:
  		eth0:						#eth0 从dhcp服务器获取ip地址
  			dhcp4: yes/true
  		eth1:						#eth1 设置静态地址
  			dhcp4: no/false
  			addresses: [192.168.1.2/24]
  			gateway4: 192.168.1.1
  			nameservers:
  				addresses: [8.8.8.8, 9.9.9.9]
  			
  #尝试配置
  sudo netplan try 
  
  sudo netplan (--debug) apply
  ```

- 重启网络服务

  ```shell
  #on a Ubuntu desktop
  sudo systemctl restart network-manager
  #on a Ubuntu Server
  sudo systemctl restart system-networkd
  ```

  
