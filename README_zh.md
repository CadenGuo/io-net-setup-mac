# [Enligsh](./README.md)/中文

# 在 mac 上配置 io.net 节点

本文描述了如何在 Mac 主机（必须是 m1 或 m2 CPU）上以更简单的方式设置 IO net 节点。此方法无需安装 docker desktop（在 Mac 上体积庞大且运行缓慢）。我们可以在终端中完成所有步骤。
在本文中，我们使用 Colima 来作为 我们的 Linux 虚拟机引擎，为 Docker 提供 Linux 运行时。

# 先决条件
 - 拥有一台 Mac 机器（带有 m1 或 m2 CPU）。
 - 拥有一个 io.net 账户，并按照 io.net 上的工作设置流程进行操作。步骤如下：
    1. 前往 [io.net](https://io.net/) 并点击右上角的 `Sign up`。
    2. 前往 https://cloud.io.net/worker/devices 并点击右上角的 `Connect New Worker`。
    3. 填写表单直至显示“3. Run the command to connect device”步骤出现。然后停止（注意官方步骤中的”5. Prerequisites for Mac“也不需要执行）。

## 步骤
本文 docker-desktop（官方步骤推荐使用该软件）本文使用一种更轻量级的 Linux 运行时， [colima](https://github.com/abiosoft/colima)。

### 打开 Mac 的终端
搜索“终端”并打开，接下来所有操作都在终端中进行。

### 安装一些工具
```bash
brew install colima
brew install docker # 这不是 Docker Desktop。它只是 Docker 命令行工具。
```

### 启动 Linux 运行时
```bash 
# 我们这里分配比较多的资源，因为 io.net 可能会分配一些任务，并且镜像本身已经非常大了
colima start --cpu 8 --memory 16 --disk 50
```

### 下载 io.net 二进制文件
```bash
curl -L https://github.com/ionet-official/io_launch_binaries/raw/main/launch_binary_mac -o launch_binary_mac
chmod +x launch_binary_mac
```

### 运行容器（开始跑节点）
复制步骤“3. Run the command to connect device”中的命令，该步骤在之前浏览器的“Add New Devices”的步骤里面（参考先决条件）。
大概是一下格式的一条命令：
./launch_binary_mac --device_id=xxxxxx --user_id=xxxxx --operating_system="macOS" --usegpus=false --device_name=guo-kaitong
每次添加新的节点时，命令都会不同。

将复制的命令粘贴到终端中并按回车键。等待命令返回。

### 验证容器是否运行
5-10 分钟后，使用以下命令检查容器状态：
```bash
docker ps
```
应该能看到 2 个正在运行的容器，如下所示（CONTAINER ID 和 NAMES 列将不同）：
```
CONTAINER ID   IMAGE                               COMMAND                  CREATED             STATUS             PORTS     NAMES
d4b2059f4060   ionetcontainers/io-worker-monitor   "tail -f /dev/null"      About an hour ago   Up About an hour             goofy_vaughan
a81cc2d5f904   ionetcontainers/io-worker-vc        "sudo -E /srp/invoke…"   About an hour ago   Up About an hour             friendly_meninsky
```
之后节点就设置完成了，如果没有其他问题（网络，等等），页面上的状态也应该会变为running。