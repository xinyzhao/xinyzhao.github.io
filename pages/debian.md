# Debian 12.13

## 安装系统

* 使用 debian-12.13.0-amd64-DVD-1.iso 进行安装
* 选择 english，地区选择 Hong Kong，键盘选择 American English
* Configure the package manager / Popularity 选择 No
* 安装包选择 SSH server 和 standard system utilities

## 配置 APT

```bash
# 切换到 root 用户
su -
# 备份原来的源列表
cp /etc/apt/sources.list /etc/apt/sources.list.bak
# 更新源列表
cat > /etc/apt/sources.list << 'EOF'
deb https://mirrors.aliyun.com/debian bookworm main contrib non-free non-free-firmware
deb https://mirrors.aliyun.com/debian bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.aliyun.com/debian-security bookworm-security main contrib non-free non-free-firmware
EOF
# 更新系统
apt update && apt upgrade -y
# 安装 sudo
apt install sudo -y
# 将用户添加到 sudo 组
usermod -aG sudo `xxx`
# 退出当前会话，重新登录以使 sudo 权限生效。
```

## 安装 UFW

```bash
sudo apt install ufw -y
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

## 安装 ZSH

```bash
sudo apt install curl git wget vim zsh -y
# 切换默认 shell 为 zsh
chsh -s $(which zsh)
# 重新登录以使更改生效
# 安装oh-my-zsh
git clone https://gitee.com/mirrors/oh-my-zsh.git ~/.oh-my-zsh
# 配置zshrc
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
# 安装高亮插件
git clone https://gitee.com/dawnwords/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
# 自动补全插件
git clone https://gitee.com/lhaisu/zsh-autosuggestions.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
# 编辑zshrc
vim ~/.zshrc
```

> [Oh My Zsh 主题预览](https://github.com/ohmyzsh/ohmyzsh/wiki/themes)

```bash
# 设置主题
ZSH_THEME="agnoster"
# 添加插件
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
    z
)
```

```bash
# 使环境变量生效
source ~/.zshrc
```

## 安装 1Panel

安装位置选择当前用户下的1panel目录，防止权限不足，安装完成后，保存访问地址、端口、用户名、密码

```bash
mkdir -p ~/1panel && cd ~/1panel
curl -sSL https://resource.fit2cloud.com/1panel/package/quick_start.sh -o quick_start.sh
sudo bash quick_start.sh
```

```bash
# 开启ufw，允许1Panel端口
sudo ufw allow `1Panel端口`/tcp
# 配置 Docker 国内镜像源
sudo vim /etc/docker/daemon.json
```

```json
{
    "registry-mirrors": [
        "https://docker.1ms.run",
        "https://docker.1panel.live",
        "https://docker.xuanyuan.me"
    ]
}
```

```bash
# 重启 Docker 服务
sudo systemctl restart docker
# 将当前用户添加到 docker 组
sudo usermod -aG docker `whoami`
```

## 安装 xfce + xrdp

```bash
sudo apt install xfce4 xfce4-goodies xorg dbus-x11 x11-xserver-utils xrdp -y
# 配置xrdp（适配xfce4，解决远程桌面黑屏）
sudo mv /etc/xrdp/startwm.sh /etc/xrdp/startwm.sh.bak
sudo tee /etc/xrdp/startwm.sh > /dev/null << 'EOF'
#!/bin/sh
unset DBUS_SESSION_BUS_ADDRESS
unset XDG_RUNTIME_DIR

. /etc/X11/Xsession
startxfce4
EOF
```

```bash
# 赋予执行权限
sudo chmod +x /etc/xrdp/startwm.sh
# 将当前用户添加到 ssl-cert 组
sudo usermod -aG ssl-cert `whoami`
# 添加ufw规则，允许xrdp端口
sudo ufw allow 3389/tcp && sudo ufw reload
# 重启 xrdp 服务
sudo systemctl restart xrdp && sudo systemctl enable xrdp
# 服务器优化（关闭休眠，提升稳定性）
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
# 安装 firefox 浏览器
sudo apt install firefox-esr -y
# 安装中文语言包和字体
sudo apt install -y fonts-wqy-microhei fonts-wqy-zenhei fonts-noto-cjk
# 刷新字体缓存
sudo fc-cache -fv
```

## 安装 WiFi 驱动

下载 AIC8800 WiFi 模块的驱动：

```bash
# 下载驱动压缩包
curl https://www.tenda.com.cn/prod/api/data/center/download/541288615886917/690937177473093 -o aic8800_linux_drvier.zip
# 解压压缩包
sudo apt install unzip -y && unzip aic8800_linux_drvier.zip
# 进入驱动目录，修改权限
cd 源码/Appendix/linux_driver_sourcecode/aic8800_linux_drvier && chmod +x *.sh
# 执行安装脚本
sudo ./install_setup.sh
# 编译安装驱动
sudo apt install build-essential
cd drivers/aic8800 && make && sudo make install
```

查看 USB 设备列表，确认 WiFi 模块的设备 ID

```bash
lsusb
```

显示为MSC表示 USB 无线网卡被识别成了u盘

```bash
Bus 001 Device 003: ID a69c:5721 aicsemi Aic MSC
```

安装模式切换工具

```bash
sudo apt install usb-modeswitch usb-modeswitch-data -y
```

你的 VID/PID：a69c:5721，完整切换指令：

```bash
sudo usb_modeswitch -v a69c -p 5721 -M "5553424312345678000000000000061b000000020000000000000000000000"
```

执行完不要关闭终端，观察输出提示 switch success 即成功。查看网卡接口：

```bash
ip a
# 或
ip link
```

出现 wlan0 即网卡已正常识别。

永久自动切换（以后插电自动变网卡，不用每次手动输命令）

```bash
# 创建系统自动切换配置文件：
sudo nano /usr/local/bin/aic-boot-switch.sh
```

粘贴下面全部内容：

```bash
#!/bin/bash
# 开机延迟3秒，避开系统抢占
sleep 3
/usr/sbin/usb_modeswitch -v a69c -p 5721 -M "5553424312345678000000000000061b000000020000000000000000000000"
```

按 Ctrl+O 保存、Ctrl+X 退出。

```bash
# 赋予执行权限
sudo chmod +x /usr/local/bin/aic-boot-switch.sh
# 设置 开机自动运行（最高优先级）
sudo crontab -e
```

在最后一行添加

```bash
@reboot /usr/local/bin/aic-boot-switch.sh
```

保存后重启系统：

```bash
sudo reboot
```

插电后等待几秒，查看网卡接口，确认自动切换成功

```bash
# 查看 USB 设备列表，确认自动切换成功
lsusb
# 查看网卡接口
ip a
```

安装 NetworkManager 并连接 WiFi：

```bash
# 安装 NetworkManager
sudo apt install -y network-manager
# 安装完后启动并开机自启：
sudo systemctl enable --now NetworkManager
# 扫描 WiFi 列表
sudo nmcli device wifi list
# 连接 WiFi，替换 SSID 和密码
sudo nmcli device wifi connect "SSID名称" password "WiFi密码"
```
