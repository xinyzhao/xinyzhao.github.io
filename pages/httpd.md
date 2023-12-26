# httpd file service

## 安装 httpd

```shell
sudo yum install httpd
```

## 修改 httpd.conf

```shell
sudo vim /etc/httpd/conf/httpd.conf
```

## 修改端口

```xml
Listen 80
```

## 修改路径

```xml
DocumentRoot "/var/www/html"
```

```xml
<Directory "/var/www">
</Directory>
```

```xml
<Directory "/var/www/html">
</Directory>
```

## 修改 welcome.conf

```shell
sudo vim /etc/httpd/conf.d/welcome.conf
```

将 Options -Indexes 改成 Options +Indexes

```xml
<LocationMatch "^/+$">
    Options +Indexes
    ErrorDocument 403 /.noindex.html
</LocationMatch>
```

## 启动 httpd

```shell
sudo systemctl start httpd
sudo systemctl enable httpd
```

## 错误：You don't have permission to access / on this server.

```
chmod 755 /var/www
chmod 755 /var/www/html
```