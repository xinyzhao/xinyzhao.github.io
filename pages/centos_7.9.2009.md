# CentOS 7.9.2009

**目录**
[TOC]

## 操作系统

### CentOS 下载安装

1. [CentOS 7.9.2009 镜像下载](https://mirrors.aliyun.com/centos-vault/7.9.2009/isos/x86_64/)

2. 使用 [Rufus](https://rufus.ie/zh/) 或 [Ventoy](https://www.ventoy.net/cn/download.html) 制作启动盘并安装

3. [CentOS 7 最小化安装后的必备操作](https://blog.csdn.net/sinat_38967565/article/details/106879278)

### 修改网卡IP

  ```sh
  # 查看网卡名称
  ip addr

  # 编辑网卡配置文件
  vi /etc/sysconfig/network-scripts/ifcfg-ens33
  ```

  ```sh
  TYPE=Ethernet
  PROXY_METHOD=none
  BROWSER_ONLY=no
  BOOTPROTO=static          # 修改为static表示使用静态IP
  DEFROUTE=yes
  IPV4_FAILURE_FATAL=no
  IPV6INIT=yes
  IPV6_AUTOCONF=yes
  IPV6_DEFROUTE=yes
  IPV6_FAILURE_FATAL=no
  NAME=ens33
  DEVICE=ens33
  ONBOOT=yes                # 开机自启
  IPADDR=192.168.x.x        # 设置静态IP地址
  NETMASK=255.255.255.0     # 子网掩码
  GATEWAY=192.168.x.1       # 默认网关
  DNS1=8.8.8.8              # 主DNS服务器
  DNS2=114.114.114.114      # 备用DNS服务器
  ```

  ```sh
  # 重启网络服务
  systemctl restart network

  # 验证配置
  ip addr
  ping -c 4 www.baidu.com
  ```

### 配置yum源

[参考1](https://www.cnblogs.com/hzke/p/17849772.html)
[参考2](https://www.cnblogs.com/lanjianhua/p/18357189)

  ```sh
  # 备份本地yum源
  cd /etc/yum.repos.d/
  mkdir backup
  mv *.repo backup/
  # 查看系统版本
  cat /etc/redhat-release
  # 配置阿里云yum源
  curl -O https://mirrors.aliyun.com/repo/Centos-7.repo
  # 配置epel库
  curl -O https://mirrors.aliyun.com/repo/epel-7.repo
  # 清除缓存
  yum clean all
  # 生成缓存
  yum makecache
  ```

### 网络配置

  ```sh
  # 安装网络工具包和vim
  sudo yum install -y net-tools vim

  # 临时关闭防火墙
  systemctl stop firewalld
  # 永久关闭防火墙
  systemctl disable firewalld

  # 临时关闭SELinux 
  setenforce 0
  # 永久关闭SELinux
  vi /etc/selinux/config
  # 将SELINUX=enforcing修改为SELINUX=disabled
  SELINUX=disabled
  ```

### 配置ssh

  ```sh
  # 编辑ssh配置文件
  vi /etc/ssh/sshd_config
  
  # 配置端口
  Port 2222                 # 修改默认端口以增加安全性
  # 登录限制
  PermitRootLogin no        # 禁用root用户登录
  PermitEmptyPasswords no   # 禁用空密码登录
  PasswordAuthentication no # 禁用密码登录（使用密钥对认证）

  # 重启ssh服务
  sudo systemctl restart sshd
  ```

### 添加新用户

  ```sh
  # 创建新用户
  useradd -m -s /bin/bash YOUR_USERNAME
  # 为新用户设置密码
  passwd YOUR_USERNAME
  # 将用户添加到 sudo 组
  usermod -aG wheel YOUR_USERNAME
  # 验证用户是否已添加到 sudo 组
  groups YOUR_USERNAME
  # 编辑sudoers
  visudo

  # 为新用户添加sudo权限,需要密码验证
  YOUR_USERNAME  ALL=(ALL)       ALL
  # 或者允许无密码执行sudo(不推荐)
  # YOUR_USERNAME  ALL=(ALL)       NOPASSWD: ALL

  # 测试新用户sudo权限
  su - YOUR_USERNAME
  sudo whoami  # 应该输出 root
  ```

## 应用环境

### zsh + oh my zsh

  ```sh
  # 安装zsh和git
  sudo yum -y install zsh git
  # 设置为zsh
  sudo chsh -s /bin/zsh
  # 安装oh my zsh
  git clone https://gitee.com/mirrors/oh-my-zsh.git ~/.oh-my-zsh
  # 配置zshrc
  cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
  # 安装高亮插件
  git clone https://gitee.com/dawnwords/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
  # 自动补全插件
  git clone https://gitee.com/lhaisu/zsh-autosuggestions.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
  # 目录跳转插件
  git clone https://gitee.com/gentlecp/autojump.git
  cd autojump
  ./install.py
  # 编辑zshrc
  vim ~/.zshrc
  ```

  > [oh my zsh 主题预览](https://github.com/ohmyzsh/ohmyzsh/wiki/themes)

  ```sh
  # 设置主题
  ZSH_THEME="agnoster"
  # 添加插件
  plugins=(
        git
        zsh-autosuggestions
        zsh-syntax-highlighting
        autojump
        z
  )
  ```

  ```sh
  # 使环境变量生效
  source ~/.zshrc
  ```

### java

  ```sh
  # 检查系统是否已安装Java
  java -version
  rpm -qa | grep java

  # 安装java
  sudo yum install -y java-1.8.0-openjdk*

  # 查找安装路径
  which java
  ls -l /usr/bin/java
  ls -l /etc/alternatives/java

  # 配置JAVA_HOME环境变量
  sudo vi /etc/profile.d/java.sh
  ```

  ```sh
  export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
  export PATH=$PATH:$JAVA_HOME/bin
  export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
  ```

  ```sh
  # 使环境变量生效
  source /etc/profile
  ```

### python

  ```sh
  # 检查系统当前Python版本
  python --version
  python3 --version

  # 安装Python3和pip
  sudo yum install -y python3 python3-pip

  # 验证安装
  python3 --version
  pip3 --version

  # 创建软链接使pip命令可用（可选）
  sudo ln -s /usr/bin/pip3 /usr/bin/pip

  # 升级pip
  sudo python3 -m pip install --upgrade pip setuptools wheel -i https://mirrors.aliyun.com/pypi/simple/

  # 配置pip源为国内镜像
  pip config set global.index-url https://mirrors.aliyun.com/pypi/simple
  pip config set install.trusted-host mirrors.aliyun.com

  # 验证配置
  pip config list
  ```

### npm + nodejs

  ```sh
  # 安装 EPEL 仓库
sudo yum install -y epel-release
# 安装 Node.js 和 npm
sudo yum install -y nodejs npm
# 验证安装
node --version
npm --version

# 安装 nrm (npm registry manager)
sudo npm install -g nrm
# 查看可用的 npm 镜像源
nrm ls
# 测试各个镜像源的速度
nrm test
# 切换到淘宝镜像源
nrm use taobao
# 验证当前使用的镜像源
npm config get registry
  ```

### nginx

  ```sh
  # 安装 EPEL 仓库（如果还没安装）
  sudo yum install -y epel-release
  # 安装nginx
  sudo yum install -y nginx

  # 启动nginx
  sudo systemctl start nginx
  # 设置开机自启动
  sudo systemctl enable nginx
  # 检查 nginx 状态
  sudo systemctl status nginx

  # 如果启用了防火墙，需要开放 HTTP 端口
  sudo firewall-cmd --permanent --zone=public --add-service=http
  sudo firewall-cmd --permanent --zone=public --add-service=https
  sudo firewall-cmd --reload
  
  # 修改配置文件：
  sudo vi /etc/nginx/nginx.conf
  ```

  ```nginx
  # 设置为当前用户
  user  YOUR_USERNAME;
  # sftp 配置
  server {
    #listen 80;
    #server_name YOUR_DOMAIN www.YOUR_DOMAIN;
    
    location /sftp {
      alias /home/YOUR_USERNAME/sftp; # 文件存储路径
      autoindex on;                   # 开启目录索引
      autoindex_exact_size off;       # 显示文件大小（off时显示大概大小）
      autoindex_localtime on;         # 显示文件时间为服务器本地时间

      # 如果配置了下方的sftp.conf，则可以使用 proxy_pass 代理到 sftp.conf 中配置的地址
      proxy_pass http://sftp.YOUR_DOMAIN;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
    }
  }
  ```

  ```sh
  # 新建sftp配置文件
  sudo vi /etc/nginx/conf.d/sftp.conf
  ```

  ```nginx
  server {
    listen 80;
    server_name sftp.YOUR_DOMAIN;
    charset utf-8;  # 避免中文乱码
    location / {
      alias /home/YOUR_USERNAME/sftp; # 文件存储路径
      autoindex on;                   # 开启目录索引
      autoindex_exact_size off;       # 显示文件大小（off时显示大概大小）
      autoindex_localtime on;         # 显示文件时间为服务器本地时间
    }
  }
  ```

### mysql

  ```sh
  # 检查并删除已有的MySQL/MariaDB
  rpm -qa | grep mariadb
  sudo yum remove -y mariadb*
  rpm -qa | grep mysql
  sudo yum remove -y mysql*

  # 下载 MySQL 官方仓库 https://dev.mysql.com/downloads/repo/yum/
  sudo yum localinstall https://dev.mysql.com/get/mysql84-community-release-el7-1.noarch.rpm -y
  # 安装 MySQL 社区版
  sudo yum install -y mysql-community-server
  # 启动 MySQL 服务
  sudo systemctl start mysqld
  sudo systemctl enable mysqld
  # 获取临时密码
  sudo grep 'temporary password' /var/log/mysqld.log
  # 运行安全脚本
  sudo mysql_secure_installation
  # 登录 MySQL
  mysql -u root -p
  ```

  ```sql
  -- 创建新用户并授权(根据需要调整权限)
  CREATE USER 'YOUR_USERNAME'@'%' IDENTIFIED BY 'YOUR_PASSWORD';
  -- 授予所有权限
  GRANT ALL PRIVILEGES ON *.* TO 'YOUR_USERNAME'@'%';
  -- 或者仅授予特定权限
  -- GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO 'YOUR_USERNAME'@'%';
  -- 刷新权限
  FLUSH PRIVILEGES;
  
  -- 验证用户创建
  SELECT user,host FROM mysql.user;
  
  -- 退出
  EXIT;
  ```

  ```sh
  # 如果需要远程访问,配置防火墙
  sudo firewall-cmd --permanent --add-port=3306/tcp
  sudo firewall-cmd --reload
    
  # 验证MySQL运行状态
  sudo systemctl status mysqld
  ```

### redis

  ```sh
  # 安装 EPEL 仓库（如果还没安装）
  sudo yum install -y epel-release
  # 安装redis
  sudo yum install redis -y
  # 启动redis
  sudo systemctl start redis
  # 设置开机自启动
  sudo systemctl enable redis
  # 查看redis状态
  sudo systemctl status redis
  # 如果启用了防火墙，需要开放 Redis 端口（如果需要远程访问）
  sudo firewall-cmd --permanent --add-port=6379/tcp
  sudo firewall-cmd --reload
  # 测试Redis连接
  redis-cli ping
  # 在Redis命令行中，输入ping，您应该看到回复：PONG，
  # 输入exit退出Redis命令行
  ```

### postgresql + postgis

  ```sh
  # Install the repository RPM:
  sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

  # Install PostgreSQL:
  sudo yum install -y postgresql14 postgresql14-contrib postgresql14-server

  # Optionally initialize the database and enable automatic start:
  sudo /usr/pgsql-14/bin/postgresql-14-setup initdb

  # 启动并设置开机自启
  sudo systemctl enable postgresql-14
  sudo systemctl start postgresql-14
  # 检查状态
  sudo systemctl status postgresql-14

  # 切换用户
  sudo su - postgres

  # 进入数据库
  psql
  # 修改密码
  ALTER USER postgres WITH PASSWORD 'postgres';
  # 退出数据库命令：\q 或 exit 或 quit
  \q

  # 修改 pg_hba.conf
  sudo vim /var/lib/pgsql/14/data/pg_hba.conf
  # 在文件末尾添加以下内容
  host  all  all  0.0.0.0/0  md5

  # 修改 postgresql.conf
  sudo vim /var/lib/pgsql/14/data/postgresql.conf
  # 监听所有地址，改为 * 或者 0.0.0.0
  listen_addresses = '*'

  # 重启服务
  sudo systemctl restart postgresql-14

  # 查看和postgresql版本对应的postgis
  sudo yum list | grep postgis
  # 安装和postgresql版本对应的postgis
  sudo yum install -y postgis33_14

  # 登录数据库
  psql -U postgres -h 127.0.0.1
  # 安装扩展
  CREATE EXTENSION IF NOT EXISTS postgis;
  # 退出数据库命令：\q 或 exit 或 quit
  \q
  ```

### minio

  ```sh
  # 创建必要的目录和用户
  sudo useradd -r minio-user -s /sbin/nologin
  sudo mkdir -p /opt/minio/{data,config}
  sudo chown -R minio-user:minio-user /opt/minio

  # 下载MinIO二进制文件
  curl -O https://dl.min.io/server/minio/release/linux-amd64/minio
  sudo chmod +x minio
  sudo mv minio /usr/local/bin/

  # 创建环境变量配置文件
  sudo vi /etc/default/minio
  ```

  ```minio
  # MinIO 服务的配置
  MINIO_ROOT_USER=minio
  MINIO_ROOT_PASSWORD=minio
  MINIO_VOLUMES="/opt/minio/data"
  MINIO_OPTS="--address :9000 --console-address :9001"
  ```

  ```sh
  # 创建systemd服务文件
  sudo vi /etc/systemd/system/minio.service
  ```

  ```sh
  [Unit]
  Description=MinIO Object Storage
  Documentation=https://docs.min.io
  Wants=network-online.target
  After=network-online.target

  [Service]
  User=minio-user
  Group=minio-user
  WorkingDirectory=/opt/minio
  EnvironmentFile=/etc/default/minio
  ExecStart=/usr/local/bin/minio server $MINIO_VOLUMES $MINIO_OPTS
  Restart=always
  LimitNOFILE=65536
  TimeoutStopSec=infinity
  SendSIGKILL=no

  [Install]
  WantedBy=multi-user.target
  ```

  ```sh
  # 重新加载systemd
  sudo systemctl daemon-reload
  sudo systemctl enable minio

  # 启动服务并检查状态
  sudo systemctl start minio
  sudo systemctl status minio

  # 开放防火墙端口（如果启用了防火墙）
  sudo firewall-cmd --permanent --zone=public --add-port=9000/tcp
  sudo firewall-cmd --permanent --zone=public --add-port=9001/tcp
  sudo firewall-cmd --reload

  # 完成后，你可以通过以下地址访问：
  # API: http://127.0.0.1:9000
  # WebUI: http://127.0.0.1:9001
  ```

### docker

  ```sh
  # 卸载旧版本
  sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

  # 安装必要的依赖
  sudo yum install -y yum-utils device-mapper-persistent-data lvm2

  # 添加Docker仓库 https://download.docker.com/linux/centos/docker-ce.repo 或者镜像
  sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

  # 安装Docker
  sudo yum install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

  # 启用并启动Docker
  sudo systemctl enable docker
  sudo systemctl start docker

  # 查看Docker版本
  docker --version

  # 配置镜像加速
  sudo mkdir -p /etc/docker
  sudo tee /etc/docker/daemon.json <<-'EOF'
  {
    "registry-mirrors": [
      "https://docker.1ms.run",
      "https://docker.xuanyuan.me"
    ],
    "dns": ["8.8.8.8", "114.114.114.114"],
    "log-driver": "json-file",
    "log-opts": {
      "max-size": "100m",
      "max-file": "3"
    }
  }
  EOF

  # 重启Docker服务
  sudo systemctl daemon-reload
  sudo systemctl restart docker

  # 将当前用户添加到docker组（可选，需要重新登录生效）
  sudo usermod -aG docker $USER

  # 验证安装
  docker --version
  docker compose version
  docker run hello-world
  ```

  > [Docker命令大全](https://www.runoob.com/docker/docker-command-manual.html)

## 应用程序

### 文件管理系统：[alist](https://alist.nn.ci/zh/guide/)

  ```sh
  # 创建并进入目录
  mkdir alist && cd alist
  # 下载并执行安装脚本
  curl -O "https://alist.nn.ci/v3.sh"
  sudo bash v3.sh
  
  # 常用管理命令
  sudo systemctl start alist   # 启动服务
  sudo systemctl stop alist    # 停止服务
  sudo systemctl restart alist # 重启服务
  sudo systemctl status alist  # 查看服务状态
  
  # 获取/设置管理员密码
  cd /opt/alist
  # 查看版本
  ./alist version
  # v3.25.0以下版本
  ./alist admin
  # v3.25.0及以上版本
  ./alist admin random              # 随机生成密码
  ./alist admin set NEW_PASSWORD    # 设置指定密码，替换 NEW_PASSWORD 为你想要的密码

  # 自定义安装路径（可选）
  # 默认安装在 /opt/alist
  # 如果要安装到其他路径（例如 /root），使用以下命令：
  sudo bash v3.sh install /root

  # 其他常用命令
  # 更新 AList
  sudo bash v3.sh update
  # 卸载 AList
  sudo bash v3.sh uninstall

  # 配置nginx
  sudo vim /etc/nginx/conf.d/alist.conf
  ```

  ```nginx
  server {
    listen 80;
    server_name alist.YOUR_DOMAIN;
    location / {
      proxy_pass http://127.0.0.1:5244;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
  ```

### 版本控制系统：[gitlab](https://gitlab.com/gitlab-org/gitlab-foss)

[参考1](https://blog.csdn.net/weixin_56270746/article/details/125427722)
[参考2](https://segmentfault.com/a/1190000040220475)
[参考3](https://blog.csdn.net/unhejing/article/details/104767623)

  ```sh
  # 安装必要的依赖
  sudo yum install -y curl policycoreutils-python openssh-server perl
  sudo systemctl enable sshd
  sudo systemctl start sshd

  # 配置防火墙
  sudo firewall-cmd --permanent --add-service=http
  sudo firewall-cmd --permanent --add-service=https
  sudo firewall-cmd --reload

  # 安装 Postfix 用于邮件通知
  sudo yum install -y postfix
  sudo systemctl enable postfix
  sudo systemctl start postfix

  # 创建目录并进入
  mkdir gitlab && cd gitlab
  # 添加 GitLab 仓库
  curl -O https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh
  sudo sh script.rpm.sh

  # 安装 GitLab
  sudo yum install -y gitlab-ce

  # 配置 GitLab
  sudo vi /etc/gitlab/gitlab.rb

  # 修改以下配置
  external_url 'http://192.168.5.128:8929'    # 修改端口号
  nginx['listen_port'] = 8929
  gitlab_rails['time_zone'] = 'Asia/Shanghai' # 时区设置

  # 初始化 GitLab
  sudo gitlab-ctl reconfigure

  # 启动所有服务
  sudo gitlab-ctl start

  # 更新防火墙规则
  sudo firewall-cmd --permanent --add-port=8929/tcp
  sudo firewall-cmd --reload

  # 验证端口是否开放
  sudo firewall-cmd --list-ports

  # 查看初始密码（root用户）
  sudo cat /etc/gitlab/initial_root_password

  # 检查服务是否正常运行
  sudo gitlab-ctl status
  sudo netstat -tlnp | grep 8929

  # 常用管理命令
  sudo gitlab-ctl start          # 启动所有服务
  sudo gitlab-ctl status         # 查看服务状态
  sudo gitlab-ctl stop           # 停止所有服务
  sudo gitlab-ctl restart        # 重启所有服务
  sudo gitlab-ctl reconfigure    # 重新配置
  sudo gitlab-ctl tail           # 查看日志
  ```

  ```sh
  # 配置nginx反向代理
  sudo vi /etc/nginx/conf.d/gitlab.conf
  ```

  ```nginx
  server {
    listen 80;
    server_name gitlab.YOUR_DOMAIN;
    location / {
      proxy_pass http://127.0.0.1:8929;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
  ```

### 项目管理系统：[planka](https://planka.app/)

  ```sh
  # 安装 Docker 和 Docker Compose
  sudo yum install -y docker docker-compose

  # 创建并进入目录
  mkdir planka && cd planka

  # 下载文件 https://raw.githubusercontent.com/plankanban/planka/master/docker-compose.yml
  curl -O https://raw.githubusercontent.com/plankanban/planka/master/docker-compose.yml
  # 或者使用 https://gh-proxy.com/ 加速
  curl -O https://gh-proxy.com/raw.githubusercontent.com/plankanban/planka/master/docker-compose.yml

  # 生成随机密钥并复制
  openssl rand -hex 64

  # 编辑 docker-compose.yml 文件
  vim docker-compose.yml

  # 将 SECRET_KEY 替换为随机密钥
  SECRET_KEY=xxx
  # 将 BASE_URL 替换为您的域名和端口
  BASE_URL=https://127.0.0.1:3000
  # 配置管理员账号
  - DEFAULT_ADMIN_EMAIL=YOUR_EMAIL # Do not remove if you want to prevent this user from being edited/deleted
  - DEFAULT_ADMIN_PASSWORD=YOUR_PASSWORD
  - DEFAULT_ADMIN_NAME=YOUR_NAME
  - DEFAULT_ADMIN_USERNAME=YOUR_USERNAME

  # 启动项目
  docker-compose up -d
  # 停止项目
  docker-compose down
  # 停止并删除现有容器和数据
  docker-compose down -v
  # 查看日志
  docker-compose logs planka
  # 重启项目
  docker-compose restart planka

  # 配置nginx反向代理
  sudo vi /etc/nginx/conf.d/planka.conf
  ```

  ```nginx
  server {
      listen 80;
      server_name planka.YOUR_DOMAIN;

      location / {
          proxy_pass http://127.0.0.1:3000;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
      }
  }
  ```

  ```sh
  # 重启nginx使配置生效
  sudo systemctl restart nginx
  ```

END.
