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

    