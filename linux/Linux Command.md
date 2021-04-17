# Linux Command

### Screen命令

```shell
$ Screen command
-A 　将所有的视窗都调整为目前终端机的大小。
-d <作业名称> 　将指定的screen作业离线。
-h <行数> 　指定视窗的缓冲区行数。
-m 　即使目前已在作业中的screen作业，仍强制建立新的screen作业。
-r <作业名称> 　恢复离线的screen作业。
-R 　先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
-s 　指定建立新视窗时，所要执行的shell。
-S <作业名称> 　指定screen作业的名称。
-v 　显示版本信息。
-x 　恢复之前离线的screen作业。
-ls或--list 　显示目前所有的screen作业
```

### Software management

```shell
$ apt install XXX
$ apt remove XXX 卸载XXX，保留配置文件
$ apt purge XXX  卸载XXX，删除配置文件
$ apt update
#更新所有软件包
$ apt upgrade
#查看apt upgrade时升级的软件包
$ sudo apt-mark showmanual
#升级时忽略XXX
$ sudo apt-mark hold XXX
#恢复
$ sudo apt-mark unhold XXX
#自动卸载不被需要的依赖
$ apt autoremove
```

### firewall--ufw

```shell
#启用or禁用
$ sudo ufw enable | disable
#允许访问/禁止访问
$ sudo ufw [delete] allow | deny  [service|port|from host|proto tcp from x.x.x.x to x.x.x.x port XX]
#查看状态
$ sudo ufw status
```





























