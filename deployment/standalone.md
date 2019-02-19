# 独立部署

使用`GF`开发的应用程序可以独立地部署到服务器上，设置为后台守护进程运行即可。这种模式常用在简单的API服务项目中。

服务器我们推荐使用`*nix`服务器系列(包括:`Linux`, `MacOS`, `*BSD`)，以下使用`Ubuntu`系统为例，介绍如何部署使用`GF`框架开发的项目。

## `nohup`
我们可以使用简单的`nohup`命令来运行应用程序，使其作为后台守护进程运行，即使远程连接的SSH断开也不会影响程序的执行。在流行的Linux发行版中往往都默认安装好了`nohup`命令工具。
命令如下：
```
nohup ./gf-app &
```

## `tmux`
`tmux`是一款Linux下的终端复用工具，可以开启不同的终端窗口来将应用程序作为后台守护进程执行，即使远程连接的SSH断开也不会影响程序的执行。
在`ubuntu`系统下直接使用`sudo apt-get install tmux`安装即可。使用以下步骤将应用程序后台运行。
1. `tmux new -s gf-app`；
1. 在新终端窗口中执行`./gf-app`即可；
1. 使用`crt` + `B & D`快捷键可以退出当前终端窗口；
1. 使用`tmux attach -t gf-app`可进入到之前的终端窗口；

## `supervisor`
`supervisor`是用`Python`开发的一套通用的进程管理程序，能将一个普通的命令行进程变为后台`daemon`，并监控进程状态，异常退出时能自动重启。官方网站：http://supervisord.org/
常见配置如下：
```
[program:gf-app]
user=root
command=/var/www/main
stdout_logfile=/var/log/gf-app-stdout.log
stderr_logfile=/var/log/gf-app-stderr.log
autostart=true
autorestart=true
```
使用步骤如下：
1. 使用`sudo service supervisor start`启动`supervisor`服务；
1. 创建应用程序配置文件`/etc/supervisor/conf.d/gf-app.conf`, 内容如上;
1. 使用`sudo supervisorctl`进入`supervisor`管理终端；
1. 使用`reload`重新读取配置文件并重启当前`supoervisor`管理的所有进程；
1. 也可以使用`update`重新加载配置(默认不重启)，随后使用`start gf-app`启动指定的应用程序；
1. 使用`status`查看当前`supervisor`管理的进程状态；
