# Debian 12.13

## 安装系统

* 使用 debian-12.13.0-amd64-DVD-1.iso 进行安装
* 选择 english，地区选择 Hong Kong，键盘选择 American English
* 选择中国大陆的镜像源
* 安装包选择 SSH server 和 standard system utilities

## 安装 sudo

```bash
apt install sudo -y
# 将当前用户添加到 sudo 组
usermod -aG sudo `whoami`
```

退出当前会话，重新登录以使 sudo 权限生效。

## 更新 aliyun 源

```bash
# 备份原来的源列表
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
# 安装 vim 编辑器
sudo apt install vim -y
# 编辑源列表
sudo vim /etc/apt/sources.list
```

将文件内容替换为以下内容：

```bash
deb https://mirrors.aliyun.com/debian bookworm main contrib non-free non-free-firmware
deb https://mirrors.aliyun.com/debian bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.aliyun.com/debian-security bookworm-security main contrib non-free non-free-firmware
```

更新系统

```bash
sudo apt update
sudo apt upgrade -y
```

## 安装 UFW

```bash
sudo apt install ufw
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

## 安装 ZSH

```bash
sudo apt install curl git wget zsh -y
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

```bash
sudo apt install curl -y
curl -sSL https://resource.fit2cloud.com/1panel/package/quick_start.sh -o quick_start.sh
sudo bash quick_start.sh
```

安装位置选择当前用户下的1panel目录，防止权限不足，安装完成后，保存访问地址、端口、用户名、密码，最后添加ufw规则：

```bash
sudo ufw allow `1Panel端口`/tcp
```

配置 Docker 国内镜像源：

```bash
vim /etc/docker/daemon.json
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

重启 Docker 服务：

```bash
sudo systemctl restart docker
```

## 安装 WiFi 驱动

下载 AIC8800 WiFi 模块的驱动：

```bash
# 下载驱动压缩包
curl -O https://sftp.auroramicro.cn/aic/aic8800_linux_drvier.zip
# 解压压缩包
sudo apt install unzip -y && unzip aic8800_linux_drvier.zip
# 进入驱动目录，修改权限
cd aic8800_linux_drvier && chmod +x *.sh
# 执行安装脚本
sudo ./install_setup.sh
# 编译安装驱动
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

执行完不要关闭终端，观察输出提示 switch success 即成功。

查看网卡接口：

```bash
ip a
# 或
ip link
```

出现 wlan0 即网卡已正常识别。

永久自动切换（以后插电自动变网卡，不用每次手动输命令）
创建系统自动切换配置文件：

```bash
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
