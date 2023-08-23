# 安装 [iTerm2](https://iterm2.com/)

[https://iterm2.com/downloads.html](https://iterm2.com/downloads.html)

# 安装 [Oh my zsh](https://ohmyz.sh/)

### curl 安装方式

    sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

### wget 安装方式

    sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

# 安装 [PowerLine](http://powerline.readthedocs.io/en/latest/installation.html)
安装powerline的方式依然简单，也只需要一条命令：

    pip install powerline-status --user

或者

    pip3 install powerline-status --user

# 安装 [PowerFonts](https://github.com/powerline/fonts)

安装字体库需要首先将项目git clone至本地，然后执行源码中的install.sh。

    git clone https://github.com/powerline/fonts.git --depth=1

    cd fonts

    ./install.sh

安装好字体库之后，我们来设置iTerm2的字体，具体的操作是iTerm2 -> Preferences -> Profiles -> Text，在Font区域选中Change Font，然后找到Meslo LG字体。有L、M、S可选.

# 安装[配色方案(Color Schemes)](https://iterm2colorschemes.com/)

### 先下载配色方案

* [iTerm2-Color-Schemes](https://github.com/mbadolato/iTerm2-Color-Schemes)
* [Dracula](https://draculatheme.com/iterm)
* [Gruvbox](https://github.com/morhetz/gruvbox-contrib/tree/master/iterm2) `(Recommend)`
* [Solarized](https://github.com/altercation/solarized)

### 然后在iTerm2中安装

1. iTerm2 > Preferences > Profiles > Colors Tab
2. Open the Color Presets... drop-down in the bottom right corner
3. Select Import... from the list
4. Select the Dracula.itermcolors file
5. Select the Dracula from Color Presets...

# 安装[主题(Themes)](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)


### 下载agnoster主题，执行脚本安装：

    git clone https://github.com/fcamblor/oh-my-zsh-agnoster-fcamblor.git

    cd oh-my-zsh-agnoster-fcamblor/

    ./install

### 打开zshrc

    vi ~/.zshrc

### 将ZSH_THEME改为agnoster

    ZSH_THEME="agnoster"

# 安装插件

### 下载插件

    cd ~/.oh-my-zsh/custom/plugins/
    git clone https://github.com/zsh-users/zsh-autosuggestions.git
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git

### 打开zshrc

    vi ~/.zshrc

### 在 plugins 内添加插件

    plugins=(
            git
            zsh-autosuggestions
            zsh-syntax-highlighting
            )

# 应用 .zshrc

    source ~/.zshrc

# 调整Status Bar

* 可以在Profiles选项卡，Session页面最底部看到开启选项。Status bar enabled 选项，勾选上即可打开。点击右边的 Configure Status Bar 按钮可设置显示的内容。

* 在Preference页面中点击Appearance选项卡，可以设置Status bar的位置，修改 Status bar location，我这里改到Bottom底部。