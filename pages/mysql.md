# MySQL

### 先检查系统是否安装有 mysql

```shell
yum list installed mysql*
rpm –qa | grep mysql*
```

### 查看有没有安装源

```shell
yum list mysql*
```

### 确定系统版本

```shell
cat /etc/redhat-release
```

打开  **[MySQL Yum Repository](https://dev.mysql.com/downloads/repo/yum/)** 页面，找到对应的系统版本，点击download

在 **[No thanks, just start my download.](https://dev.mysql.com/get/mysql80-community-release-el7-11.noarch.rpm)** 上右键复制连接，或直接点击下载

### 安装源

```shell
yum -y install mysql80-community-release-el7-11.noarch.rpm
```

### 安装 mysql

```shell
yum -y install mysql-community-server
```

### 启动 mysql

```shell
sudo systemctl start mysqld
sudo systemctl enable mysqld
```

### 初始化数据库密码

```shell
grep "password" /var/log/mysqld.log
```

登录 mysql，直接复制输入密码错误，原因是!?属于特殊字符，要加 \ 转义，写成 `\! \?` 才能成功识别。

```shell
mysql -uroot -p********
```

修改密码，mysql默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于8位。

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '********';
```

### 添加用户

```mysql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

username – 你将创建的用户名说明:

host – 指定该用户在哪个主机上可以登陆,如果是本地用户可用localhost,  如 果想让该用户可以从任意远程主机登陆,可以使用通配符%

password –  该用户的登陆密码,密码可以为空,如果为空则该用户可以不需要密码登 陆服务器

例子：

```sql
CREATE USER 'javacui'@'localhost' IDENTIFIED BY '123456';
CREATE USER 'javacui'@'172.20.0.0/255.255.0.0' IDENDIFIED BY '123456';
CREATE USER 'javacui'@'%' IDENTIFIED BY '123456';
CREATE USER 'javacui'@'%' IDENTIFIED BY '';
CREATE USER 'javacui'@'%';
```

### 授权

```sql
GRANT privileges ON databasename.tablename TO 'username'@'host';
```

privileges – 用户的操作权限,如SELECT , INSERT , UPDATE  等(详细列表见该文最后面).如果要授予所 的权限则使用ALL说明: 

databasename –  数据库名

tablename-表名,如果要授予该用户对所有数据库和表的相应操作权限则可用* 表示, 如*.*

例子:

```sql
GRANT SELECT, INSERT ON test.user TO 'javacui'@'%';
GRANT ALL ON *.* TO 'javacui'@'%';
```

用以上命令授权的用户不能给其它用户授权,如果想让该用户可以授权,用以下命令

```mysql
GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
```

### 设置与更改用户密码

```sql
SET PASSWORD FOR 'username'@'host' = PASSWORD('newpassword');
```


如果是当前登陆用户用

```sql
SET PASSWORD = PASSWORD("newpassword");
```

### 撤销用户权限

```sql
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```

例子说明: privilege, databasename, tablename – 同授权部分

```sql
REVOKE SELECT ON *.* FROM 'javacui'@'%';
```

假如你在给用户’javacui’@'%’授权的时候是这样的(或类似 的):GRANT SELECT ON test.user TO  ‘javacui’@'%’, 则在使用 REVOKE SELECT ON *.* FROM  ‘javacui’@'%’;命令并不能撤销该用户对test数据库中user表的SELECT 操作注意: 

相反,如果授权使用的是GRANT SELECT ON  *.* TO ‘javacui’@'%’;则 REVOKE SELECT ON test.user FROM  ‘javacui’@'%’;命令也不能撤销该用户对test数据库中user表的 Select 权限

具体信息可以用命令SHOW GRANTS FOR ‘javacui’@'%’; 查看

### 删除用户

```sql
DROP USER ‘username’@'host’;
```


### 操作后切记刷新数据库

```sql
flush privileges;
```
