# 实验二 基于腾讯云创建个人网站 

####                                                                                                  何俊杰（116072017001）

## 一.使用WordPress创建个人网站 

### 部署环境：LAMP

- 云操作系统：CentOS 7.2 64位；
- HTTP服务器：Apache Web 服务器；
- 数据库：MySQL；
- 建站工具：WordPress（基于PHP）

#### **1.安装Apache Web服务器**

使用yum工具安装:

```
sudo yum install httpd
```

安装完成之后，启动Apache Web服务器：

```
sudo systemctl start httpd.service
```

测试Apache服务器是否成功运行:

```
http://your_cvm_ip/
```

出现如下界面：

![](I:../image/013.png)

#### 2.**安装MySQL**

安装MariaDB：

```
sudo yum install mariadb-server mariadb
```

![](I:../image/014.png)

启动mariadb：

```
sudo systemctl start mariadb
```

![](I:../image/015.png)

运行简单的安全脚本以移除潜在的安全风险，启动交互脚本：

```
sudo mysql_secure_installation
```

设置开机启动MariaDB：

```
sudo systemctl enable mariadb.service
```

![](I:../image/016.png)

#### **3.安装PHP**

安装Remi和EPEL这两个仓库：

```
sudo yum install epel-release yum-utils
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

![](I:../image/017.png)

![](I:../image/018.png)

启用PHP 7.2 Remi仓库：

```
sudo yum-config-manager --enable remi-php72
```

安装PHP以及php-mysql：

```
sudo yum install php php-mysql
```

查看安装的php版本：

```
php -v
```

![](I:../image/019.png)

重启Apache服务器以支持PHP：

```
sudo systemctl restart httpd.service
```

##### 安装PHP模块

为了更好的运行PHP，需要启动PHP附加模块，使用如下命令可以查看可用模块：

```
yum search php-
```

![](I:../image/020.png)

WordPress使用php-gd进行图片的缩放，安装php-fpm和php-gd

```
sudo yum install php-fpm php-gd
```

重启Apache服务：

```
sudo service httpd restart
```

#### **4.测试PHP**

这里我们利用一个简单的信息显示页面（info.php）测试PHP。创建info.php并将其置于Web服务的根目录（/var/www/html/）：

```
sudo vim /var/www/html/info.php
```

添加如下内容：

```
<?php phpinfo(); ?>
```

完成之后，使用刚才获取的cvm的IP地址，在你的本地主机的浏览器中输入:

```
http://your_cvm_ip/info.php
```

可看到如下界面：

![](I:../image/021.png)

#### **5.安装WordPress以及完成相关配置**

##### **(1)为WordPress创建一个MySQL数据库**

以root用户登录MySQL数据库：

```
mysql -u root -p
```

![](I:../image/022.png)

键入之前设置的root用户密码，并按下回车键进入MySQL命令行模式

首先为WordPress创建一个新的数据库：

```
CREATE DATABASE wordpress;
```

接着为WordPress创建一个独立的MySQL用户：

```
CREATE USER rusb@localhost IDENTIFIED BY '84141203';
```

![](I:../image/023.png)

授权给wordpressuser用户访问数据库的权限：

```
GRANT ALL PRIVILEGES ON wordpress.* TO rusb@localhost IDENTIFIED BY '84141203';
```

![](I:../image/024.png)

刷新MySQL的权限：

```
FLUSH PRIVILEGES;
```

![](I:../image/025.png)

最后退出MySQL的命令行模式：

```
exit
```

##### **(2)安装WordPress**

下载WordPress至当前用户的主目录：

```
cd ~
wget http://wordpress.org/latest.tar.gz
```

解压该文件：

```
tar xzvf wordpress-5.2.4-zh_CN.tar.gz
```

![](I:../image/030.png)

解压之后在主目录下产生一个wordpress文件夹。我们将该文件夹下的内容同步到Apache服务器的根目录下，使得wordpress的内容能够被访问。这里使用rsync命令：

```
sudo rsync -avP ~/wordpress/ /var/www/html/
```

接着在Apache服务器目录下为wordpress创建一个文件夹来保存上传的文件：

```
mkdir /var/www/html/wp-content/uploads
```

对Apache服务器的目录以及wordpress相关文件夹设置访问权限：

```
sudo chown -R apache:apache /var/www/html/*
```

##### **(3)配置WordPress**

首先通过命令行连接WordPress和MySQL，定位到wordpress所在文件夹：

```
cd /var/www/html
```

WordPress的配置依赖于wp-config.php文件，当前该文件夹下并没有该文件，我们通过拷贝wp-config-sample.php文件来生成：

```
cp wp-config-sample.php wp-config.
```

然后，通过nano超简单文本编辑器来修改配置，主要是MySQL相关配置：

```
nano wp-config.php
```

![](I:../image/026.png)

(4)通过Web界面进一步配置WordPress**

输入你的IP地址或者域名：49.235.126.200

出现如下界面：

![](I:../image/027.png)

设置网站的标题，用户名和密码以及电子邮件等，点击**Install WordPress**，弹出确认页面

弹出登录界面：

![](I:../image/028.png)

输入用户名和密码之后，进入WordPress的控制面板：

![](I:../image/029.png)