# 完成EXVS_Server布置

## 1.事先准备

下载源代码到服务器上进行布置

下载dotnet7.0.0，到服务器上安装，安装教程如下：

[微软dotnet]: https://dotnet.microsoft.com/zh-cn/download/dotnet	"下载地址"

下载。NET 7.0，Server中使用这个版本进行编译

上传到服务器后

```bash
mkdir -p $HOME/dotnet && tar zxf dotnet-sdk-7.0.410-linux-x64.tar.gz -C $HOME/dotnet
export DOTNET_ROOT=$HOME/dotnet
export PATH=$PATH:$HOME/dotnet
```

------

前面的命令只会使 .NET SDK 命令可用于运行它的终端会话。

可以编辑 shell 配置文件来永久添加命令。存在多个不同的 shell 可用于 Linux，并且每个 shell 都有不同的配置文件。例如:

- Bash Shell: ~/.bash_profile, ~/.bashrc
- Korn Shell: ~/.kshrc or .profile
- Z Shell: ~/.zshrc or .zprofile

编辑 shell 的相应源文件，并将 `:$HOME/dotnet` 添加到现有 `PATH` 语句的末尾。如果不包含 `PATH` 语句，则使用 `export PATH=$PATH:$HOME/dotnet` 添加一个新行。

此外，将 `export DOTNET_ROOT=$HOME/dotnet` 添加到文件末尾。

------

安装完成后：

```bash
dotnet --version #查看版本号
```



## 2.编译Server

安装完成后：

cd 到Server文件下：

```bash
dotnet build
dotnet run
```

编译运行，查看是否能正常运行起来，运行起来就进行下一步，将其放到后端也能运行

## 3.后台进程保护

Systemd方法：

1. **创建Systemd服务单元文件**：首先，创建一个以 `.service` 结尾的 Systemd 服务单元文件。你可以在 `/etc/systemd/system/` 目录下创建这个文件，例如 `myapp.service`。

```bash
cd /etc/systemd/system/
touch exvsserver.service
vim exvsserver.service
```

里面内容如下：

```bash
[Unit]
Description=EXVS2Xboost Server
After=network-online.target syslog.target
Wants=network-online.target

[Service]
User=root	#运行用户名
WorkingDirectory=/root/EXVS2-POC-2.0.0/Server #指令运行位置
ExecStart=/root/dotnet/dotnet run #运行指令
Restart=on-abnormal #自动重启
RestartSec=5 #重启间隔

[Install]
WantedBy=multi-user.target
```

**重新加载Systemd配置**：保存并关闭文件后，使用以下命令重新加载 Systemd 配置：

```bash
sudo systemctl daemon-reload
```

- 启动服务：`sudo systemctl start myapp`
- 停止服务：`sudo systemctl stop myapp`
- 重启服务：`sudo systemctl restart myapp`
- 查看服务状态：`sudo systemctl status myapp

```bash
sudo systemctl start exvsserver.service
sudo systemctl stop exvsserver.service
sudo systemctl restart exvsserver.service
sudo systemctl status exvsserver.service
```

开机自启动(也可以设置到之前自启动处)：

```bash
sudo systemctl enable myapp
```

