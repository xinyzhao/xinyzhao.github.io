# Crontab

`crontab` 是一个用于设置周期性执行任务的工具, 通常用于周期性的备份、日志清理等任务。

## 基本命令

```bash
# 查看当前用户的 crontab 任务
crontab -l

# 编辑当前用户的 crontab 任务
crontab -e

# 删除当前用户的 crontab 任务
crontab -r

# 查看指定用户的 crontab 任务
crontab -l -u username

# 编辑指定用户的 crontab 任务
crontab -e -u username

# 删除指定用户的 crontab 任务
crontab -r -u username
```

## 基本使用

`crontab` 的配置文件格式如下:

```bash
# crontab 格式说明:
# ┌───────────── 分钟 (0 - 59) 
# │ ┌───────────── 小时 (0 - 23)
# │ │ ┌───────────── 日期 (1 - 31)
# │ │ │ ┌───────────── 月份 (1 - 12)
# │ │ │ │ ┌───────────── 星期 (0 - 6) (星期日=0)
# │ │ │ │ │
# * * * * *  <执行的命令>

# 常用示例:

# 每分钟执行一次
* * * * * command

# 每小时执行一次(整点执行)
0 * * * * command

# 每天凌晨 2 点执行
0 2 * * * command

# 每周日凌晨 2 点执行
0 2 * * 0 command 

# 每月 1 日凌晨 2 点执行
0 2 1 * * command

# 每 15 分钟执行一次
*/15 * * * * command

# 工作日(周一至周五)每天 8 点执行
0 8 * * 1-5 command
```

特殊字符说明:

- `*`: 代表所有可能的值
- `/`: 代表"每"的间隔频率
- `-`: 代表范围
- `,`: 代表列举

## 系统服务

`crontab` 服务的启动、停止、重启等操作:

```bash
# 启动 crontab 服务
systemctl start crond

# 停止 crontab 服务
systemctl stop crond

# 重启 crontab 服务
systemctl restart crond

# 查看 crontab 服务状态
systemctl status crond

# 开机自启动
systemctl enable crond

# 禁止开机自启动
systemctl disable crond
```
