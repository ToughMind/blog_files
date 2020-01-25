---
title: "unix常用命令"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["linux"]
categories: ["linux"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---

## 系统
### 配置
命令 | 说明
---|---
sudo chown -R git:git /home/git | 修改权限
hostnamectl set-hostname <主机名>  | 修改主机名
ifconfig -a | 查看内网ip
curl ifconfig.me | 查看外网ip
cat /etc/redhat-release | 查看当前系统
uname -m | 查看多少位系统
`lspci | grep -i nvidia` |查看gpu型号
uname –r | 查看内核
`dpkg --list | grep nvidia-*` <BR> cat /proc/driver/nvidia/version <BR> nvidia-smi | 查看nvidia显卡版本

### 进程
命令 | 说明
---|---
ps -aux | 查看进程
pgrep、pidof、killall | 杀死进程
netstat -tunlp |grep 22<BR>netstat -a -t --numeric-ports -p | 查看进程端口号被占用
 
### 软件包
> ubuntu检查软件包是否安装
- `dpkg -s XXX`
- `dpkg-query -l XXX`
- `dpkg -l | grep XXX`

### wget
> 使用wget下载需要用户名和密码访问的网站资源
```
--http-user=USER设置HTTP用户 
--http-passwd=PASS设置HTTP密码
```

### 缓存
> 清理内存
```
echo 1 > /proc/sys/vm/drop_caches
echo 2 > /proc/sys/vm/drop_caches
echo 3 > /proc/sys/vm/drop_caches
```

### 清除swap
```
swapoff -a
swapon -a
``` 

## centos7
### rpm
命令 | 说明
---|---
rpm -qa /rpm -ql name | 查看某个软件安装在哪个目录

### yum
命令 | 说明
---|---
yum search name | 查找指定软件包
yum list | 列出所有可安装的软件包 
yum list updates | 列出所有可更新的软件包
yum list extras | 列出所有已安装但不在Yum Repository软件包资源库内的软件包
yum list name <BR> yum info name | 列出指定的软件包安装信息
yum update name | 更新软件包
yum update | 更新系统
yum remove | 卸载软件包

## virtualenvwrapper和virtualenv
命令 | 说明
---|---
workon <BR> lsvirtualenv | 列出虚拟环境列表
mkvirtualenv name | 新建虚拟环境
workon name | 启动/切换虚拟环境
rmvirtualenv name | 删除虚拟环境
deactivate | 离开虚拟环境

## pyenv和virtualenv
命令 | 说明
---|---
pyenv versions | 查看当前pyenv下的所有python版本
pyenv install --list | 列出所有可以下载的python版本
pyenv install 版本号 | 下载并安装python版本
python global 版本号 | 切换当前默认的python版本，全局有效.
python local 版本号 | 切换当前默认的python版本，当前用户有效
pyenv rehash | 重建环境变量，每当你增删 Python 版本或带有可执行文件的包（如 pip）以后，都应该执行一次本命令
pyenv virtualenv 版本 环境名称 | virtualenv创建虚拟环境
pyenv virtualenvs | virtualenv查看已创建的虚拟环境
pyenv activate 环境名称 | pyenv切换虚拟环境
pyenv deactivate | 退出当前环境
pyenv virtualenv-delete 环境名称 | 删除虚拟环境
pyenv local 环境名称 | 在特定文件夹中设定环境

## mongodb
命令 | 说明
---|---
systemctl stop mongod | 关闭
mongo 127.0.0.1:27017 | 连接mongodb并指定端口
mongo 127.0.0.1:27017/test | 连接到指定的mongodb数据库
mongo 127.0.0.1:27017/test -u user -p password |  指定用户名和密码连接到指定的mongodb数据库

## Redis
命令 | 说明
---|---
redis-cli -h 127.0.0.1 -p 6379 | 远程服务连接
redis-cli -h 127.0.0.1 -p 6379 -a 123456 | 有权限控制时(加上-a 密码)

## supervisor
命令 | 说明
---|---
supervisorctl stop name |停止某一个进程，name 为 [program:x] 里的 x
supervisorctl start name | 启动某个进程
supervisorctl restart name | 重启某个进程
supervisorctl stop groupworker: | 结束所有属于名为 groupworker 这个分组的进程 (start，restart 同理)
supervisorctl stop groupworker:name1 | 结束 groupworker:name1 这个进程 (start，restart 同理)
supervisorctl stop all | 停止全部进程，注：start、restart、stop 都不会载入最新的配置文件
supervisorctl reload | 载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程
supervisorctl update | 根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启
kill -9 $(ps -ef|grep supervisor | awk '{print $2}') <BR> kill -9 `ps -ef|grep supervisor | awk '{print $2}'` | 关闭

## pip
命令 | 说明
---|---
pip install name==2.8.7 | 安装指定版本包
pip show --files name | 查看已安装的包
pip list --outdated <BR> pip list -o | 检查需要更新的包
pip install --upgrade name <BR> pip install -U <包名> | 升级包
pip freeze > path/requirements.txt | 导出requirements.txt
pip show -f name | 显示包所在的目录
pip search name | 搜索包
pip freeze | 生成一个类似需要安装的包的列表
  
### pip包网址
[https://pypi.org/simple/XXXXXX/](https://pypi.org/simple/XXXXXX/)

## npm、nrm
命令 | 说明
---|---
npm install -g nrm  | 下载nrm
nrm ls | 列出可用软件源
nrm use taobao | 使用淘宝
 
### 配置代理
```
npm config set proxy http://127.0.0.1:3128
npm config set http-proxy http://127.0.0.1:3128
npm config set https-proxy https://127.0.0.1:3128
```

## apt
命令 | 说明
---|---
apt-get autoremove  | 卸载
dpkg -l <BR> apt list --installed | 查看已下载
apt-get install -y apt-transport-https | 增加源使用https
apt-cache madison <BR>apt-cache policy <BR>apt-cache search | 查看版本

### 执行add-apt-repository需要的包
`apt-get install software-properties-common python-software-properties
`

### 卸载nginx
```
sudo apt-get remove nginx-*
sudo apt-get purge nginx-*
# 删除旧文件目录
sudo find /etc -name "*nginx*" |xargs  rm -rf
```

## tar
### 打包压缩
命令 | 说明
---|---
tar -cvf 名字.tar 文件 |  仅打包，不压缩
tar -zcvf 名字.tar.gz 文件 | 打包后，以gzip压缩
tar -jcvf 名字.tar.bz2 文件 | 打包后，以bzip2压缩
tar -Zcvf 名字.tar.Z 文件 | 打包后，以compress压缩|rar格式的压缩，需要先下载rar for linux
zip 名字.zip 文件 | zip格式的压缩，需要先下载zip for linux


### 查阅
命令 | 说明
---|---
tar -tvf 名字.tar|
tar -ztvf 名字.tar.gz|
tar -jtvf 名字.tar.bz2|


### 解压
命令 | 说明
---|---
tar -xvf 名字.tar |
tar -zxvf 名字.tar.gz |
tar -jxvf 名字.tar.bz2|
tar -Zxvf 名字.tar.Z  |
unrar -e 名字.rar |
unzip 名字.zip | 

## 特殊
- 备份 /home和/etc,但不要 /home/no
```
tar --exclude /home/no -zcvf myfile.tar.gz /home/* /etc
```
- -C参数解压到其他路径
```
tar -xvf file.tar -C 路径
```