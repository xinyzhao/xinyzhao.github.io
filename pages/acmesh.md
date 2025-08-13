# [acme.sh](https://github.com/acmesh-official/acme.sh)

## 官方 wiki

* [English](https://github.com/acmesh-official/acme.sh/wiki)
* [中文说明](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)

## 安装 acme.sh

```bash
# 官方脚本安装
curl https://get.acme.sh | sh -s yourname@example.com

# 国内 gitee 源 安装
git clone https://gitee.com/neilpang/acme.sh.git
cd acme.sh
./acme.sh --install -m yourname@example.com

# 使配置生效
source ~/.bashrc
# 或
source ~/.zshrc

# 使用 LetsEncrypt 作为默认的 CA 服务器。
acme.sh --set-default-ca --server letsencrypt

# 使用 HTTP 验证，Nginx 模式 生成证书
acme.sh --issue --nginx -d example.com -d www.example.com

# 安装证书 Nginx 模式
acme.sh --install-cert -d example.com  -d www.example.com --key-file /path/to/key.pem --fullchain-file /path/to/cert.pem --reloadcmd "service nginx force-reload"
```

[使用 DNS 验证](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

* [aliyun](https://ram.console.aliyun.com/users)

```bash
# 将获取到的 key 和 secret 配置到环境变量中
export Ali_Key="<key>"
export Ali_Secret="<secret>"
# 生成证书
acme.sh --issue --dns dns_ali -d example.com -d *.example.com
```

## 配置 nginx

```bash
# HTTP 服务器配置 (80端口)
server {
    listen 80;
    server_name example.com www.example.com;  # 替换为你的域名
    
    # 可选：将所有HTTP请求重定向到HTTPS
    return 301 https://$host$request_uri;
}

# HTTPS 服务器配置 (443端口)
server {
    listen 443 ssl;
    server_name example.com www.example.com;  # 与HTTP配置相同的域名
    
    # SSL证书配置
    ssl_certificate /path/to/your/certificate.crt;  # 替换为证书路径
    ssl_certificate_key /path/to/your/private.key;  # 替换为私钥路径
    
    # SSL相关优化配置
    ssl_protocols TLSv1.0 TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    
    # 网站根目录
    root /var/www/example.com;
    index index.html index.htm;
    
    # 日志配置
    access_log /var/log/nginx/example.com.access.log;
    error_log /var/log/nginx/example.com.error.log;
    
    # 其他网站配置
    location / {
        try_files $uri $uri/ =404;
    }
}
```

```bash
# 重新加载nginx配置
sudo systemctl reload nginx
```

## 证书操作

```bash
# 查看所有证书
acme.sh --list
# 查看证书信息
acme.sh --info -d example.com
# 查看证书到期时间
acme.sh --expiry -d example.com
# 查看证书更新计划
crontab  -l
# 手动更新证书
acme.sh --renew -d example.com --force
# 删除证书
acme.sh --remove -d example.com --keydir /path/to/key/dir
```