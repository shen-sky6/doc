### 1. 下载 MySQL 8.0.30 tar 包(可换成8.4.5TEL)

首先，使用 `wget` 命令下载 MySQL 8.0.30 的 tar 包：​

bash

复制编辑

`wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.30-linux-glibc2.12-x86_64.tar.xz`

请注意，下载链接中的版本号和操作系统架构可能会有所不同，请根据实际情况选择合适的版本。​

### 2. 解压 tar 包

下载完成后，解压 tar 包：​[dev.mysql.com+1MySQL Community Downloads+1](https://dev.mysql.com/doc/mysql-installation-excerpt/8.0/en/binary-installation.html?utm_source=chatgpt.com)

`tar -xvf mysql-8.0.30-linux-glibc2.12-x86_64.tar.xz`

解压后，您可以将其移动到 `/usr/local/` 目录下：​


`mv mysql-8.0.30-linux-glibc2.12-x86_64 /usr/local/mysql`

### 3. 创建 MySQL 用户和组

为了安全起见，建议创建一个专门的 MySQL 用户和组：​

`groupadd mysql useradd -r -g mysql mysql`

### 4. 初始化数据目录

初始化 MySQL 数据目录：​[usavps.com+1dev.mysql.com+1](https://usavps.com/blog/29491/?utm_source=chatgpt.com)


`cd /usr/local/mysql bin/mysqld --initialize --user=mysql`

执行该命令后，您会在终端输出中看到一个临时的 root 密码，请妥善保存。​

### 5. 设置环境变量

为了方便使用 MySQL 命令，建议将 MySQL 的 `bin` 目录添加到系统的 `PATH` 环境变量中：​

`echo "export PATH=/usr/local/mysql/bin:$PATH" >> ~/.bash_profile source ~/.bash_profile`

### 6. 启动 MySQL 服务

启动 MySQL 服务：​[ramoradba.com+1MySQL Community Downloads+1](https://ramoradba.com/2022/10/02/install-mysql-5-7-community-server-using-rpm-bundle-centos-7/?utm_source=chatgpt.com)


`cd /usr/local/mysql bin/mysqld_safe --user=mysql &`

该命令将在后台启动 MySQL 服务。​

### 7. 设置 root 密码

使用临时密码登录 MySQL：​


`bin/mysql -u root -p`

系统会提示您输入临时密码，登录后，您可以修改 root 密码：​

sql

复制编辑

`ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';`

请将 `'new_password'` 替换为您希望设置的新密码。​

### 8. 配置 MySQL 开机自启

为了让 MySQL 在系统启动时自动启动，您可以创建一个 systemd 服务单元文件：​


`vi /etc/systemd/system/mysql.service`

在文件中添加以下内容：​


`[Unit] Description=MySQL Community Server After=network.target  [Service] Type=simple User=mysql Group=mysql ExecStart=/usr/local/mysql/bin/mysqld_safe ExecStop=/usr/local/mysql/bin/mysqladmin shutdown LimitNOFILE = 5000  [Install] WantedBy=multi-user.target`

保存并退出编辑器后，重新加载 systemd 配置：​

bash

复制编辑

`systemctl daemon-reload`

启用并启动 MySQL 服务：​

bash

复制编辑

`systemctl enable mysql systemctl start mysql`

这样，MySQL 就会在系统启动时自动启动。

# MySQL8.0允许外部访问
二、开始修改配置：
1，登进MySQL之后，

2，输入以下语句，进入mysql库：

use mysql
3，更新域属性，'%'表示允许外部访问：

update user set host='%' where user ='root';
4，执行以上语句之后再执行：

FLUSH PRIVILEGES;
5，再执行授权语句：

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
然后外部就可以通过账户密码访问了。

6，其它说明：

FLUSH PRIVILEGES; 命令本质上的作用是：

将当前user和privilige表中的用户信息/权限设置从mysql库(MySQL数据库的内置库)中提取到内存里。

MySQL用户数据和权限有修改后，希望在"不重启MySQL服务"的情况下直接生效，那么就需要执行这个命令。

通常是在修改ROOT帐号的设置后，怕重启后无法再登录进来，那么直接flush之后就可以看权限设置是否生效。

而不必冒太大风险。

三、可能存在的其它问题：
执行完之后，再用Navicat连接mysql，报错如下：

Client does not support authentication protocol requested by server；
报错原因：

mysql8.0 引入了新特性 caching_sha2_password；这种密码加密方式Navicat 12以下客户端不支持；

Navicat 12以下客户端支持的是mysql_native_password 这种加密方式；

解决方案：

1，用如下语句查看MySQL当前加密方式

select host,user,plugin from user;
查询结果

+-----------+------------------+-----------------------+
| host      | user             | plugin                |
+-----------+------------------+-----------------------+
| %         | root             | caching_sha2_password |
| localhost | mysql.infoschema | mysql_native_password |
| localhost | mysql.session    | mysql_native_password |
| localhost | mysql.sys        | mysql_native_password |
+-----------+------------------+-----------------------+
看第一行，root加密方式为caching_sha2_password。

2，使用命令将他修改成mysql_native_password加密模式：

update user set plugin='mysql_native_password' where user='root';
再次连接的时候，就成功了。
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
                        
原文链接：https://blog.csdn.net/h996666/article/details/80921913