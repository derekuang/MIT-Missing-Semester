# Command-line Environment



## 一、任务控制

Shell会使用UNIX提供的信号机制来执行进程间通信。

信号是一种软件中断，进程收到一个信号时，有多种不同的行为，如停止执行、处理信号并基于信号传递的信息来改变执行。

- 结束进程

  可以通过`SIGINT(Ctrl-C)` `SIGQUIT(Ctrl-\)` `SIGTERM(kill命令)` 信号停止程序，但程序也可以对信号进行捕获。

- 暂停和后台执行进程

  `SIGSTOP` 暂停进程

  `SIGTSTP(Ctrl-Z)` terminal版本的`SIGSTOP`

  `SIGKILL` 不能被进程捕获并结束进程

  `jobs` 获取当前终端会话中未完成的任务

  `fg` `bg` 将进程挪到前台/后台进行

  `nohup` 进程忽略挂起信号(`SIGHUP`关闭终端时可触发)运行

  `disown` 从当前shell中移除进程(这样就不会被shell关闭时的挂起信号影响)



## 二、终端多路复用

`tmux` 最流行的终端多路器，基于面板和标签分割多个终端窗口，以便可以同时和多个shell会话交互

- 会话（每个会话都是一个独立的工作区，包含一个或多个窗口）
  - `tmux` 新建一个会话
  - `tmux new -s NAME` 以指定名称开始一个新的会话
  - `tmux ls` 列出所有会话
  - `<C-b> d` 分离(detach)当前会话
  - `tmux a` 重新连接最后一个会话
  - `tmux a -t NAME` 连接指定会话
- 窗口（相当于浏览器的标签页，视觉上将会话分割成多个部分）
  - `<C-d>` 关闭窗口
  - `<C-b>` 
    - c (create)新建窗口
    - N 跳转到第N个窗口
    - p (previous)跳转到前一个窗口
    - n (next)跳转到下一个窗口
    - , 重命名窗口
    - w 列出当前所有窗口
- 面板（分屏功能,，在一个屏幕显示多个shell）
  - `<C-b>` 
    - " 水平分割
    - % 垂直分割
    - <方向键> 切换到指定方向面板
    - z (zoom)切换当前面板缩放
    - [ 往回卷动屏幕
    - <空格> 在不同面板排布切换

[tmux快速入门教程]: https://hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/
[tmux进阶教程]: http://linuxcommand.org/lc3_adv_termmux.php

`screen` 大多数UNIX系统中都默认安装的终端多路复用器



## 三、别名

```shell
alias alias_name="command_to_alias arg1 arg2"
```

正常情况下，我们需要把别名配置放在shell启动文件中，如`.bashrc`



## 四、配置文件（点文件）

[shell启动脚本配置]: https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html

对bash来说，可以通过`.bashrc`或`.bash_profile`来进行配置启动脚本

管理配置文件的正确姿势：它们应该集中放在点文件的文件夹下，并使用版本控制系统进行管理，然后通过脚本将其 **符号链接** 到需要的地方。（对于可移植性需求，可以用if语句根据不同设备编写不同的配置）



## 五、远端设备

当我们需要使用远程服务器提供服务时，就需要使用`SSH`（安全shell）

```shell
ssh user@remote
```

- 执行命令

  ssh可以直接远程执行命令，如：`ssh user@remote ls /`

- SSH密钥

  公钥验证机制，向服务端证明客户端持有对应私钥即可验证，省去密码验证中每次都需要输入密码的麻烦。可以理解为，私钥=密码。

  - 密钥生成 `ssh-keygen`

  - 基于密钥的认证机制

    ssh会查询`.ssh/authorized_keys`（存放着公钥）来确认哪些用户允许被登录

    ```shell
    cat .ssh/id_ed25519.pub | \
    ssh user@remote 'cat >> ~/.ssh/authorized_keys'
    ```

    如果支持`ssh-copy-id-i`，可以使用下面的方案

    ```shell
    ssh-copy-id -i .ssh/id_ed25519.pub user@remote
    ```

- 通过SSH复制文件

  - `ssh+tee` `cat localfile | ssh remote tee remotefile`

    将内容写入远程文件

  - `scp` `scp path/to/localfile remote:path/to/remotefile`

    大量拷贝文件或目录

  - `rsync`

    `scp` 改进版，可检测两端文件防止重复拷贝，还提供一些像符号连接、权限管理、基于`--partial`的断点续传功能

- 端口转发（监听远程服务器端口的技术）

  - 本地端口转发 `ssl -L sourcePort:forwardToHost:onPort connectToHost`

    `soucePort` 本机的源端口

    `forwardToHost` 目标主机（如果是`localhost`，是指代远程主机）

    `onPort` 目标主机的端口

    `connectToHost` 与本机进行ssh连接的远程主机，可以访问目标主机（远程主机和目标主机可以是同一台主机）

    - 数据从本地的`soucePort`端口出发，通过ssh连接达到`connectToHost`，然后被**转发**到`forwardToHost`的`onPort`端口

  - 远程端口转发 `ssh -R sourcePort:forwardToHost:onPort connectToHost`

    `soucePort` 远程主机的源端口

    `forwardToHost` 目标主机（如果是`localhost`，是指代本机）

    `onPort` 目标主机的端口

    `connectToHost` 与本机进行ssh连接的远程主机，可以访问目标主机（远程主机和目标主机可以是同一台主机）

    - 和本地端口转发反过来，数据从`connectToHost`出发，通过ssh连接到达本地，然后被**转发**到`forwardToHost`的`onPort`端口

  - SSH配置

    一个本地连接远端服务器进行本地端口转发的例子

    `ssh -i ~/.id_ed25519 --port 2222 -L 9999:localhost:8888 foobar@remote_server`

    可以用配置文件`~/.ssh/config`保留这些选项，方便命令直接读取

    ```
    Host vm
        User foobar
        HostName remote_server
        Port 2222
        IdentityFile ~/.ssh/id_ed25519
        LocalForward 9999 localhost:8888
    ```

    `/etc/ssh/sshd_config` 服务器端配置常用位置

  - 杂项

    - `Mosh` 对ssh进行了改进，允许连接漫游、间歇连接和智能本地回显
    - `sshfs` 可以将远程文件夹挂载到本地



## 六、作业

- 任务控制

  1. 我们可以使用类似 `ps aux | grep` 这样的命令来获取任务的 pid ，然后您可以基于pid 来结束这些进程。但我们其实有更好的方法来做这件事。在终端中执行 `sleep 10000` 这个任务。然后用 `Ctrl-Z` 将其切换到后台并使用 `bg`来继续允许它。现在，使用 [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 来查找 pid 并使用 [`pkill`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 结束进程而不需要手动输入pid。(提示：: 使用 `-af` 标记)。

     ```shell
     pgrep -af "sleep 10000" | awk '{print $2}' | xargs pkill
     ```

  2. 如果您希望某个进程结束后再开始另外一个进程， 应该如何实现呢？在这个练习中，我们使用 `sleep 60 &` 作为先执行的程序。一种方法是使用 [`wait`](http://man7.org/linux/man-pages/man1/wait.1p.html) 命令。尝试启动这个休眠命令，然后待其结束后再执行 `ls` 命令。

     但是，如果我们在不同的 bash 会话中进行操作，则上述方法就不起作用了。因为 `wait` 只能对子进程起作用。之前我们没有提过的一个特性是，`kill` 命令成功退出时其状态码为 0 ，其他状态则是非0。`kill -0` 则不会发送信号，但是会在进程不存在时返回一个不为0的状态码。请编写一个 bash 函数 `pidwait` ，它接受一个 pid 作为输入参数，然后一直等待直到该进程结束。您需要使用 `sleep` 来避免浪费 CPU 性能。
     
     ```bash
     #!/bin/env bash
     
     pidwait() {
         while kill -0 "$1"
         do
             sleep 1
         done
         ls
     }
     ```

- 别名

  1. 创建一个 `dc` 别名，它的功能是当我们错误的将 `cd` 输入为 `dc` 时也能正确执行。

     ```shell
     alias dc=cd
     ```

  2. 执行 `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10`  来获取您最常用的十条命令，尝试为它们创建别名。注意：这个命令只在 Bash 中生效，如果您使用 ZSH，使用`history 1` 替换 `history`。

- 配置文件

  1. 为您的配置文件新建一个文件夹，并设置好版本控制

  2. 在其中添加至少一个配置文件，比如说您的 shell，在其中包含一些自定义设置（可以从设置 `$PS1` 开始）。

  3. 建立一种在新设备进行快速安装配置的方法（无需手动操作）。最简单的方法是写一个 shell 脚本对每个文件使用 `ln -s`，也可以使用[专用工具](https://dotfiles.github.io/utilities/)

  4. 在新的虚拟机上测试该安装脚本。

  5. 将您现有的所有配置文件移动到项目仓库里。

  6. 将项目发布到GitHub。

     ```shell
     #!/bin/env bash
     
     # get all dotfiles except .git
     files=$(ls -a $1 | grep -E '^\.[^.]+' | grep -v .git)
     
     # create symlinks
     for file in $files
     do
         if [ -f ~/$file ]; then
             echo "symlink $file already exists"
         else
             ln -s $1/$file ~/$file
         fi
     done
     ```

- 远端设备

  1. 前往 `~/.ssh/` 并查看是否已经存在 SSH 密钥对。如果不存在，请使用`ssh-keygen -o -a 100 -t ed25519`来创建一个。建议为密钥设置密码然后使用`ssh-agent`，更多信息可以参考 [这里](https://www.ssh.com/ssh/agent)；
  2. 在`.ssh/config`加入下面内容：

  ```
  Host vm
      User username_goes_here
      HostName ip_goes_here
      IdentityFile ~/.ssh/id_ed25519
      LocalForward 9999 localhost:8888
  ```

  1. 使用 `ssh-copy-id vm` 将您的 ssh 密钥拷贝到服务器。
  2. 使用`python -m http.server 8888` 在您的虚拟机中启动一个 Web 服务器并通过本机的`http://localhost:9999` 访问虚拟机上的 Web 服务器
  3. 使用`sudo vim /etc/ssh/sshd_config` 编辑 SSH 服务器配置，通过修改`PasswordAuthentication`的值来禁用密码验证。通过修改`PermitRootLogin`的值来禁用 root 登录。然后使用`sudo service sshd restart`重启 `ssh` 服务器，然后重新尝试。
  4. (附加题) 在虚拟机中安装 [`mosh`](https://mosh.org/) 并启动连接。然后断开服务器/虚拟机的网络适配器。mosh可以恢复连接吗？
  5. (附加题) 查看`ssh`的`-N` 和 `-f` 选项的作用，找出在后台进行端口转发的命令是什么？

