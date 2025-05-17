HotCRP部署文档
=================================
本文档旨在说明在阿里云服务器（Ubuntu22.04系统）上部署HotCRP的详细步骤，帮助用户快速在本地或服务器上搭建HotCRP。

安装所需库
------------
* Nginx
```
# 安装nginx
sudo apt-get update
sudo apt-get install nginx -y

# 查看nginx版本，出现nginx版本号，即安装成功。
nginx -v

# 启动nginx
sudo systemctl start nginx

# 查看nginx状态
sudo systemctl status nginx
```

* PHP version 7.3
```
# 安装软件源拓展工具
sudo apt -y install software-properties-common apt-transport-https lsb-release ca-certificates

# 添加PHP PPA源，需要按一次回车
sudo add-apt-repository ppa:ondrej/php

# 安装PHP和对应拓展
sudo apt install -y php7.3 php7.3-fpm php7.3-intl php7.3-mysqlnd
```

* MariaDB
```
# 安装MariaDB
sudo apt install mariadb-server mariadb-client -y

# 启动MariaDB
sudo systemctl start mariadb

# 查看MariaDB状sudo systemctl status mariadb态
sudo systemctl status mariadb
```

* Postfix
```
# 安装Postfix
sudo apt install -y postfix

# 查看Posfix状态
systemctl status postfix
```

* poppler-utils, zip
```
安装poppler-utils和zip
sudo apt install -y zip poppler-utils
```

HotCRP部署
------------

