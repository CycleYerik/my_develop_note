# Introduction

basic linux commands
# Linux 常用命令速查手册 (增强版)

## 1. 文件和目录操作

*   **ls (list)**: 列出目录内容。
    *   `ls -l`: 显示详细信息。
    *   `ls -a`: 显示所有文件，包括隐藏文件。
    *   `ls -h`: 以人类可读的格式显示大小 (KB, MB, GB)。
    *   `ls -t`: 按修改时间排序，最新的在前。
    *   `ls -R`: 递归列出子目录中的内容。
    *   **常用组合**: `ls -alh`

*   **cd (change directory)**: 切换目录。
    *   `cd /path/to/dir`: 切换到指定目录。
    *   `cd ~` 或 `cd`: 切换到当前用户的主目录。
    *   `cd ..`: 切换到上一级目录。
    *   `cd -`: 切换到上一次所在的目录。

*   **pwd (print working directory)**: 显示当前工作目录的绝对路径。

*   **mkdir (make directory)**: 创建目录。
    *   `mkdir dir_name`: 创建一个目录。
    *   `mkdir -p project/src/main`: 递归创建多级目录。

*   **rm (remove)**: 删除文件或目录。
    *   `rm file.txt`: 删除一个文件 (会提示)。
    *   `rm -f file.txt`: 强制删除文件，不提示。
    *   `rm -r dir_name`: 递归删除目录及其内容 (会提示)。
    *   `rm -rf dir_name`: **(高危操作)** 强制递归删除目录，不提示。

*   **cp (copy)**: 复制文件或目录。
    *   `cp source.txt destination.txt`: 复制并重命名文件。
    *   `cp -r source_dir/ target_dir/`: 递归复制整个目录。
    *   `cp -p source.txt target_dir/`: 复制文件并保留其属性(权限、时间戳等)。

*   **mv (move)**: 移动或重命名文件/目录。
    *   `mv old_name.txt new_name.txt`: 重命名文件。
    *   `mv file.txt /path/to/dir`: 将文件移动到指定目录。

*   **touch**: 创建空文件或更新文件时间戳。
    *   `touch new_file.txt`: 如果文件不存在则创建，如果存在则更新其修改时间。

*   **ln (link)**: 创建链接文件。
    *   `ln -s /path/to/original /path/to/link`: 创建一个符号链接 (软链接)，类似 Windows 的快捷方式。

*   **find**: 在文件系统中查找文件。
    *   `find / -name "filename"`: 从根目录开始查找名为 "filename" 的文件。
    *   `find . -type f -name "*.log"`: 查找当前目录下所有 `.log` 后缀的普通文件。
    *   `find . -mtime -7`: 查找7天内被修改过的文件。
    *   `find . -size +100M`: 查找大于 100MB 的文件。
    *   `find . -type f -name "*.c" -exec grep -l "main" {} \;`: 查找所有 C 文件并打印出其中包含 "main" 的文件名。

*   **grep (Global Regular Expression Print)**: 在文件中搜索文本模式。
    *   `grep "pattern" file.txt`: 在文件中查找包含 "pattern" 的行。
    *   `grep -r "pattern" ./dir`: 递归地在目录中查找。
    *   `grep -i "pattern" file.txt`: 忽略大小写进行搜索。
    *   `grep -v "pattern" file.txt`: 显示不匹配 "pattern" 的行。
    *   `grep -c "pattern" file.txt`: 统计匹配的行数。

*   **tar**: 打包和解包文件 (常用于归档和压缩)。
    *   `tar -czvf archive.tar.gz /path/to/dir`: 将目录打包并用 gzip 压缩。
    *   `tar -xzvf archive.tar.gz`: 解压一个 .tar.gz 文件。
    *   `tar -xzvf archive.tar.gz -C /target/dir`: 解压到指定目录。
    *   `tar -tf archive.tar.gz`: 查看压缩包内容而不解压。

*   **zip / unzip**: 用于 .zip 格式的压缩和解压。
    *   `zip -r archive.zip directory/`: 压缩目录。
    *   `unzip archive.zip`: 解压文件。

---

## 2. 文本处理

*   **cat**: 查看文件全部内容。
    *   `cat file1.txt file2.txt > merged.txt`: 合并文件。

*   **less**: 分页查看文件内容 (功能比 `more` 强大，可前后翻页)。

*   **head / tail**: 查看文件的开头/结尾。
    *   `head -n 20 file.log`: 查看文件的前20行。
    *   `tail -n 20 file.log`: 查看文件的后20行。
    *   `tail -f file.log`: **(非常实用)** 实时跟踪文件的末尾更新，常用于监控日志。

*   **wc (word count)**: 统计文件的行数、单词数、字节数。
    *   `wc -l file.txt`: 只统计行数。
    *   `wc -w file.txt`: 只统计单词数。

*   **diff**: 比较两个文件的差异。
    *   `diff file1.txt file2.txt`

*   **sort**: 对文件的行进行排序。
    *   `sort file.txt`: 按字母顺序排序。
    *   `sort -n numbers.txt`: 按数值大小排序。
    *   `sort -r file.txt`: 逆序排序。

*   **uniq**: 报告或忽略重复的行 (通常与 `sort` 配合使用)。
    *   `sort file.txt | uniq -c`: 统计每行出现的次数。

*   **sed (stream editor)**: 流编辑器，用于对文本进行复杂的处理，如替换。
    *   `sed 's/old_text/new_text/g' file.txt`: 替换文件中的所有 "old_text" 为 "new_text"。

---

## 3. 系统信息与性能监控

*   **top / htop**: 实时动态地监控系统进程和资源使用情况 (`htop` 更推荐)。

*   **ps (process status)**: 显示进程快照。
    *   `ps aux`: 显示所有用户的详细进程信息。
    *   `ps -ef`: 以全格式显示所有进程。
    *   **组合使用**: `ps aux | grep "nginx"`

*   **free -h**: 以人类可读格式显示内存使用情况。

*   **df -h**: 以人类可读格式显示磁盘空间使用情况。

*   **du (disk usage)**: 估算文件和目录的磁盘使用空间。
    *   `du -sh /path/to/dir`: 查看指定目录的总大小。
    *   `du -h --max-depth=1 .`: 查看当前目录下各子目录的大小。

*   **uptime**: 显示系统运行了多长时间、当前用户数以及系统平均负载。

*   **dmesg**: 显示内核环形缓冲区的信息，用于诊断硬件问题。

*   **journalctl (systemd)**: 查看和管理 systemd 日志。
    *   `journalctl -u nginx.service`: 查看 nginx 服务的日志。
    *   `journalctl -f`: 实时跟踪所有日志。

---

## 4. 进程管理

*   **kill**: 向进程发送信号，通常用于终止进程。
    *   `kill PID`: 终止指定 PID 的进程 (默认发送 TERM 信号)。
    *   `kill -9 PID`: **(强制)** 强制杀死进程 (发送 KILL 信号)。

*   **pkill**: 根据进程名杀死进程。
    *   `pkill nginx`

*   **killall**: 杀死所有同名进程。
    *   `killall firefox`

*   **jobs**: 显示在后台运行的作业。

*   **bg / fg**: 将作业放到后台 (`bg`) 或前台 (`fg`) 运行。

---

## 5. 用户与权限管理

*   **sudo**: 以超级用户 (root) 权限执行命令。
    *   `sudo apt update`

*   **su (switch user)**: 切换用户。
    *   `su - username`: 切换到指定用户，并加载其环境变量。

*   **whoami**: 显示当前登录的用户名。

*   **passwd**: 修改用户密码。

*   **chmod (change mode)**: 修改文件或目录的权限。
    *   `chmod 755 script.sh`: 使用数字模式 (rwx)。
    *   `chmod u+x script.sh`: 使用符号模式 (为用户增加执行权限)。

*   **chown (change owner)**: 修改文件或目录的所有者和所属组。
    *   `chown user:group file.txt`

---

## 6. 网络相关

*   **ping**: 测试网络连通性。
    *   `ping -c 4 google.com`: 发送4个数据包后停止。

*   **ip / ifconfig**: 查看和配置网络接口。
    *   `ip addr show` 或 `ifconfig`: 显示 IP 地址。

*   **ss / netstat**: 显示网络连接、路由表等。
    *   `ss -tunlp`: 显示所有监听中的 TCP/UDP 端口。

*   **dig / nslookup**: DNS 查询工具。
    *   `dig google.com`

*   **curl / wget**: 命令行下载工具。
    *   `curl -O http://example.com/file.zip`: 下载文件并保存为原名。
    *   `wget -c http://example.com/large_file.iso`: 断点续传下载大文件。

*   **ssh (Secure Shell)**: 安全地远程登录到另一台主机。
    *   `ssh user@hostname`

*   **scp (secure copy)**: 在网络上的主机之间安全地复制文件。
    *   `scp file.txt user@remote_host:/remote/path`: 将本地文件复制到远程。
    *   `scp user@remote_host:/remote/file.txt .`: 将远程文件复制到本地。

---

## 7. 软件包管理

*   **Debian/Ubuntu (APT)**
    *   `sudo apt update`: 更新软件包列表。
    *   `sudo apt upgrade`: 升级所有已安装的软件包。
    *   `sudo apt install package_name`: 安装软件包。
    *   `sudo apt remove package_name`: 卸载软件包。
    *   `apt search package_name`: 搜索软件包。

*   **RedHat/CentOS (YUM/DNF)**
    *   `sudo dnf check-update`: 检查可用的更新。
    *   `sudo dnf upgrade`: 升级所有软件包。
    *   `sudo dnf install package_name`: 安装软件包。
    *   `sudo dnf remove package_name`: 卸载软件包。
    *   `dnf search package_name`: 搜索软件包。

---

## 8. Shell 与环境变量

*   **echo**: 打印文本或变量的值。
    *   `echo "Hello, World"`
    *   `echo $PATH`: 显示 PATH 环境变量。

*   **export**: 设置或导出环境变量。
    *   `export MY_VAR="my_value"`

*   **alias**: 为命令创建别名。
    *   `alias ll='ls -alh'`: 创建 `ll` 作为 `ls -alh` 的别名。

*   **history**: 显示历史命令记录。

*   **管道 `|`**: 将一个命令的输出作为另一个命令的输入。
    *   `ps aux | grep "ssh"`

*   **重定向 `>` & `>>`**:
    *   `command > output.txt`: 将命令输出覆盖写入文件。
    *   `command >> output.txt`: 将命令输出追加到文件末尾。