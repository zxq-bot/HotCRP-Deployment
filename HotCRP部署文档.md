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
# 安装poppler-utils和zip
sudo apt install -y zip poppler-utils
```

HotCRP部署
------------
首先将github上的HotCRP项目clone到服务器上
```
# 克隆HotCRP
git clone https://github.com/kohler/hotcrp

# 设置网站目录权限
sudo chmod -R o+rx /var/hotcrp (替换为代码文件夹位置)
```

1. 配置HotCRP数据库

* 给数据库的 root 用户设置密码，提高数据库服务器的安全性
   
   起始是空密码，直接回车。随后可以设置新密码。
   ```
   sudo mysql_secure_installation
   ```
* 创建会议数据库
   
   创建会议数据库的账号和密码，保存在代码文件夹下的conf/options.php中。
   ```
   sudo lib/createdb.sh --user=root --password=root_password_here
   ```

2. 配置Nginx访问HotCRP
   
   使用Nginx配置 Web 服务器，使所有对 HotCRP 站点的访问均由 HotCRP 的 `index.php` 脚本处理。Nginx配置文件为/etc/nginx/sites-enabled/default，在文件末尾插入以下内容:
   ```
   server {
       # 注意区分端口，请勿占用其他网站的端口
       listen 8080 default_server;
       server_name SNGHotCRP.com;

       # 指定网站的根目录和默认首页文件。
       root /var/hotcrp;
       index index.php;

       # 静态资源路径规则
       location ~* ^/(scripts|stylesheets|images|js|css)/ {
           access_log off;
           expires 30d;
           try_files $uri =404;
       }

       # 将所有根路径请求转发给 PHP-FPM 并使用 index.php 处理。
       location / {
           fastcgi_pass unix:/run/php/php7.3-fpm.sock;
           fastcgi_split_path_info ^(/[^/]+)(/.*)$;
           fastcgi_param SCRIPT_FILENAME /var/hotcrp/index.php;
           include fastcgi_params;
         }

       # 禁止访问 .htaccess 等以 .ht 开头的文件
       location ~ /\.ht {
           deny all;
       }
   }
   ```
   配置完后，`http://服务器IP:8080/`就能访问到部署的HotCRP网站。

3. 修改PHP相关参数

* 首先在代码文件夹下的.user.ini中修改`upload_max_filesize`、`post_max_size`以及`max_input_vars`三个参数。.user.ini文件内容如下:
  ```
  ; PHP FastCGI settings for HotCRP
  
  ; These directives limit how large a paper can be uploaded.
  ; post_max_size should be >= upload_max_filesize.
  upload_max_filesize = 15M
  post_max_size = 20M
  
  ; Some pages involve a lot of post variables.
  max_input_vars = 4096
  
  ; A large memory_limit helps when sending very large zipped files.
  memory_limit = 128M
  ```

* 接下来修改`session.gc_maxlifetime`参数

  修改/etc/php/7.3/fpm/php.ini中的`session.gc_maxlifetime`
  ```
  session.gc_maxlifetime = 86400
  ```
  在/var/hotcrp/conf/options.php中添加语句:
  ```
  $Opt["sessionLifetime"] = 86400;
  ```
   
 * 最后修改MariaDB的my.cnf

   在/etc/mysql/my.cnf中添加以下语句:
   ```
   [mysqld]
   max_allowed_packet=32M
   ```

   
   

   

   
5. 配置Postfix邮件服务










