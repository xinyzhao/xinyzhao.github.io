# nginx



## 安装 nginx

```shell
yum install nginx -y
```

## 编辑 nginx.conf

```shell
sudo vim /etc/nginx/nginx.conf
```

在 http { 中添加

```
server {
    listen	8090;
    server_name  localhost;
    location / {
        root	/home/aurora/web/dist;
        try_files $uri $uri/ @router;
        index	index.html;
        proxy_set_header Host $host;
    }
    location @router {
            rewrite ^.*$ /index.html last;
        }
    location /api {
        rewrite ^/api/(.*)$ /$1 break;
        proxy_pass http://39.107.96.7:8080;
    }
}

server {
    listen  8091;
    server_name  localhost;
    location / {
        root        /home/aurora/web/rfid;
        try_files $uri $uri/ @router;
        index       index.html;
        proxy_set_header Host $host;
    }
    location @router {
        rewrite ^.*$ /index.html last;
    }
    location /api {
        rewrite ^/api/(.*)$ /$1 break;
        proxy_pass http://39.107.96.7:8081;
    }
}
```

注释掉原有 80 端口

```
#    server {
#        listen       80;
#        listen       [::]:80;
#        server_name  _;
#	root	/usr/share/nginx/html;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#        location = /404.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#        location = /50x.html {
#        }
#    }
```

## 启动 nginx

```shell
sudo systemctl start nginx
sudo systemctl enable nginx
```

## 卸载 nginx
先查看是否启动了 nginx 服务

```shell
ps -ef | grep nginx
```

如果 nginx 启动了服务，则需要先关闭 nginx 服务 【没启动就略过这一步】

```shell
kill 进程id
```

查看所有与 nginx 有关的文件夹

```shell
sudo find / -name nginx
```


删除与 nginx 有关的文件夹

```shell
rm -rf file /usr/local/nginx*
```

卸载Nginx相关的依赖

```shell
yum remove nginx
```

这样就卸载完成了

## 其他命令

```
####端口号操作
#查询开启的所有端口
firewall-cmd --list-port
#设置80端口开启
firewall-cmd --zone=public --add-port=80/tcp --permanent
#验证80端口是否开启成功 (单个端口查询)
firewall-cmd --zone=public --query-port=80/tcp
#设置80端口关闭
firewall-cmd --zone=public --remove-port=80/tcp --permanent

####防火墙操作
#检查防火墙是否开启
systemctl status firewalld
#开机自启防火墙
systemctl enable firewalld
#开机禁止自启防火墙
systemctl disable firewalld
#启动
systemctl start firewalld
#关闭
systemctl stop firewalld
#重启
firewall-cmd --reload

```

