# [IndexTTS](https://github.com/index-tts/index-tts) 本地部署(Windows)

## 环境准备

### 安装 Git （可选）

#### 官方网站

[https://git-scm.com/](https://git-scm.com/)

#### 下载地址

[https://git-scm.com/download/win](https://git-scm.com/download/win)

### 安装 ffmpeg (可选)

#### 官方网站

[https://ffmpeg.org/](https://ffmpeg.org/download.html)

#### 下载地址

[https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-7.1.1-full_build.7z](https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-7.1.1-full_build.7z)

#### 安装步骤

- 解压缩 ffmpeg 到指定目录
- 打开命令提示符（CMD）或 PowerShell
- 执行以下命令，将 ffmpeg 目录添加到系统环境变量中

  - 添加到用户环境变量（无需管理员权限）

    ```Bash
    setx PATH "%PATH%;C:\path\to\ffmpeg\bin"
    ```

  - 添加到系统环境变量（需要管理员权限）
    - 以管理员身份打开 PowerShell
    - 执行以下命令：
    ```Bash
    setx PATH "%PATH%;C:\path\to\ffmpeg\bin"
    ```

- 重启命令提示符（CMD）或 PowerShell 使环境变量生效
- 验证 ffmpeg 安装成功

  ```bash
  ffmpeg -version
  ```

### 安装 miniconda

#### 官方网站

[https://www.anaconda.com/download/success](https://www.anaconda.com/download/success)

#### 清华大学镜像站

[https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

#### 下载地址

- 官方下载：[https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe](https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe)
- 清华大学：[https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/?C=M&O=D)
- 中科大：[https://mirrors.ustc.edu.cn/anaconda/miniconda/](https://mirrors.ustc.edu.cn/anaconda/miniconda/)

### 配置 miniconda

> 如果在安装时没有把 miniconda 加入到环境变量中，需要搜索关键词 `anaconda`，运行 `Anaconda Prompt` 或 `Anaconda PowerShell Prompt` 打开 conda 环境。

#### 验证 conda 安装成功

```bash
conda --version
```

#### 配置 pip 镜像源

##### 清华大学

```bash
pip config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```

##### 阿里云

```bash
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
```

##### 腾讯云

```bash
pip config set global.index-url https://mirrors.cloud.tencent.com/pypi/simple
```

#### 配置 conda 镜像源

通过修改用户目录下的 .condarc 文件来使用镜像站。

不同系统下的 .condarc 目录如下：

- Linux: ${HOME}/.condarc
- macOS: ${HOME}/.condarc
- Windows: C:\Users\<YourUserName>\.condarc
  > Windows 用户无法直接创建名为 .condarc 的文件，可先执行 conda config --set show_channel_urls yes 生成该文件之后再修改。

```bash
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

## 安装 IndexTTS

### 创建 conda 环境

```bash
conda create -n index-tts python=3.10
conda activate index-tts
```

### 安装 pynini

```bash
# after conda activate index-tts
conda install -c conda-forge pynini==2.1.6
pip install WeTextProcessing --no-deps
```

### 安装 PyTorch

可以通过 `nvidia-smi` 查看 CUDA 版本，选择对应版本的 PyTorch 安装。

#### 官方命令

```bash
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128
```

#### 清华大学

```bash
pip3 install torch torchvision torchaudio --index-url https://mirrors.nju.edu.cn/whl/cu128
```

否则安装 CPU 版本：

```bash
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128
```

### 下载 IndexTTS

```bash
git clone https://github.com/index-tts/index-tts.git
```

### 安装 IndexTTS

```bash
cd index-tts
pip install -e .
```

### 下载模型

```bash
pip install gradio modelscope
modelscope download --model IndexTeam/IndexTTS-1.5 --local_dir models/IndexTTS-1.5
```

### 运行 IndexTTS

```bash
# pip install -e ".[webui]" --no-build-isolation
python webui.py --model_dir models/IndexTTS-1.5
```
