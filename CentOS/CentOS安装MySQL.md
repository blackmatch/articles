# CentOS安装MySQL

* step1  
下载yum包。先从[这里](https://dev.mysql.com/downloads/repo/yum/)获取下载地址，然后通过`wget`命令下载：

```js
wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

* step2  
安装下载的yum包：

```js
sudo rpm -Uvh mysql57-community-release-el7-11.noarch.rpm
```

* step3  
查看可安装的版本，通过如下命令查看：

```js
yum repolist all | grep mysql
```

输出结果大概是这个样子的：

```js
mysql-cluster-7.5-community/x86_64 MySQL Cluster 7.5 Community    disabled
mysql-cluster-7.5-community-source MySQL Cluster 7.5 Community -  disabled
mysql-cluster-7.6-community/x86_64 MySQL Cluster 7.6 Community    disabled
mysql-cluster-7.6-community-source MySQL Cluster 7.6 Community -  disabled
mysql-connectors-community/x86_64  MySQL Connectors Community     enabled:    42
mysql-connectors-community-source  MySQL Connectors Community - S disabled
mysql-tools-community/x86_64       MySQL Tools Community          enabled:    51
mysql-tools-community-source       MySQL Tools Community - Source disabled
mysql-tools-preview/x86_64         MySQL Tools Preview            disabled
mysql-tools-preview-source         MySQL Tools Preview - Source   disabled
mysql55-community/x86_64           MySQL 5.5 Community Server     disabled
mysql55-community-source           MySQL 5.5 Community Server - S disabled
mysql56-community/x86_64           MySQL 5.6 Community Server     disabled
mysql56-community-source           MySQL 5.6 Community Server - S disabled
mysql57-community/x86_64           MySQL 5.7 Community Server     enabled:   207
mysql57-community-source           MySQL 5.7 Community Server - S disabled
mysql80-community/x86_64           MySQL 8.0 Community Server     disabled
mysql80-community-source           MySQL 8.0 Community Server - S disabled
```

* step4  
开启需要安转的版本，上一步中默认会安装的版本是`mysql57-community/x86_64`，如果想安装其他版本可以通过如下命令设置：

```js
sudo yum-config-manager --disable mysql57-community	//禁用一个版本
sudo yum-config-manager --enable mysql56-community	//开启一个版本
```

* step5  
安装MySQL：

```
sudo yum install mysql-community-server
```

> 注意:中途如果出现"Is this ok [y/d/N]"，输入`y`然后敲一下回车即可。

* step6  
启动MySQL：

```js
sudo systemctl start mysqld.service
```

查看MySQL是否启动：

```js
systemctl status mysqld
```

> 注意：如果看到`active`字样则表示MySQL已经启动了。

* step7  
查看默认的`root`用户的密码：

```js
sudo grep 'temporary password' /var/log/mysqld.log
```

输入结果应该是这样的：

```js
2017-08-31T09:47:44.838686Z 1 [Note] A temporary password is generated for root@localhost: BbdEbg-U2%2D
```

> 所以`root`用户的默认密码应该是**BbdEbg-U2%2D**

* step8  
修改`root`用户的密码。先通过如下命令使用`root`用户登录：

```js
mysql -uroot -p
```

> （输入`step7`中查到的默认密码）

登录成功后通过下面的语句修改`root`用户的密码：

```js
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

> 注意：把命令中的`MyNewPass4!`替换成你想要的密码。出现类似`Query OK, 0 rows affected (0.00 sec)`这样的输出则表示修改成功。

* step9（可选）  
加固你的MySQL。通过执行如下命令配置MySQL的安全选项：

```js
mysql_secure_installation
```

> 注意：如果你现在处于MySQL的终端，需要先通过`exit`命令退出，回到系统终端。

整个过程大致如下：

```js
Securing the MySQL server deployment.

Enter password for user root:
The 'validate_password' plugin is installed on the server.
The subsequent steps will run with the existing configuration
of the plugin.
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) :

 ... skipping.
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```

(我相信大家的英文水平应该都能看到就不一一赘述了)

* step10（可选）  
设置MySQL开机启动：

```js
sudo systemctl enable mysqld
```

