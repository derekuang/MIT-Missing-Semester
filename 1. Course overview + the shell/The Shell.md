# The Shell



## 一、什么是Shell

我们操作一台计算机（如Windows操作系统的计算机）时，通常一般人使用的是图形化界面（GUI）进行操作来调用计算机的功能，如双击点击文件夹来获取文件夹内的文件内容列表。另外，现在还可以使用语音输入、AR/VR这种新颖的交互接口操作计算机。

但是，上述的交互接口只能覆盖大部分的使用场景，你不能点击一个不存在的按钮或用语音输入一个没被录入的指令。所以，为了充分利用计算机的能力，我们需要一个原始的文字接口：Shell。

Shell的中文翻译是外壳，顾名思义指代的是计算机的外壳，作为命令行解释器，用户输入命令，Shell进行解释执行，从而实现对计算机功能的调用。



## 二、Shell的种类

不同的Shell只是提供不同的功能、语法和特性，如字体颜色、命令补全、命令历史、逻辑语句（条件、循环）、插件支持等。但他们本质上做的工作（调用系统命令）都是一样的。以下是一些常见的Shell：

- **Bourne Shell(sh)**
- **Bourne Again Shell(Bash)**
- **Z Shell(Zsh)**
- **Command Prompt(cmd)**
- **Windows PowerShell**



## 三、Shell的基本使用

不同的Shell使用上会有差别，此处以**Bash**为例子

- 常见内置变量
  - `$SHELL` `$BASH` 当前使用的Shell程序路径
  - `$PATH` 环境变量
  - `$PWD` 当前工作目录路径
  - `.` 当前目录路径
  - `..` 上级目录路径
  - `~` `$HOME`用户主目录
  - `-` 上一个工作目录路径(可以实现两个路径直接来回切换)
- 在程序间创建连接
  - `<` `>` `>>`对输入输出流重定向
    - `cat < file1 >> file2` cat程序从file1获取输入流并把输出重定向追加到file2中
  - `|` 管道操作符,将一个程序的输出和另一个程序的输入连接起来
    - `ls -l / | tail -n1` 获取根目录的详细内容列表，并显示最后一行
  - 注：创建连接的行为是通过Shell执行的，而不是内核发挥的作用，程序并不知道这些操作符的存在



## 四、作业

1. 环境要求：需要使用类Unix shell进行操作。使用`echo $SHELL`命令可以查看您的 shell 是否满足要求。
   ```shell
   # echo $SHELL
   /bin/bash
   ```

2. 在 `/tmp` 下新建一个名为 `missing` 的文件夹。
   ```shell
   # mkdir /tmp/missing
   ```

3. 用 `man` 查看程序 `touch` 的使用手册。
   ```shell
   # man touch
   ```

4. 用 `touch` 在 `missing` 文件夹中新建一个叫 `semester` 的文件。
   ```shell
   # cd /tmp/missing/
   # touch semester
   ```

5. 将以下内容一行一行地写入 `semester` 文件： 

   ```plain text
   #!/bin/sh
   curl --head --silent https://missing.csail.mit.edu
   ```

   第一行可能有点棘手， `#` 在Bash中表示注释，而 `!` 即使被双引号（`"`）包裹也具有特殊的含义。 单引号（`'`）则不一样，此处利用这一点解决输入问题。更多信息请参考  [Bash quoting 手册](https://www.gnu.org/software/bash/manual/html_node/Quoting.html)
   ```shell
   # echo \#'!'/bin/sh > semester
   # echo curl --head --silent https://missing.csail.mit.edu >> semester
   ```

6. 尝试执行这个文件。例如，将该脚本的路径（`./semester`）输入到您的shell中并回车。如果程序无法执行，请使用 `ls` 命令来获取信息并理解其不能执行的原因。

   - 因为该文件没有执行权限

7. 查看 `chmod` 的手册(例如，使用 `man chmod` 命令)
   ```shell
   # man chmod
   ```

8. 使用 `chmod` 命令改变权限，使 `./semester` 能够成功执行，不要使用 `sh semester` 来执行该程序。您的 shell 是如何知晓这个文件需要使用 `sh` 来解析呢？更多信息请参考：[shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))
   ```shell
   # chmod u+x semester
   # ./semester 
   HTTP/2 200 
   server: GitHub.com
   content-type: text/html; charset=utf-8
   last-modified: Mon, 08 Jan 2024 22:40:31 GMT
   access-control-allow-origin: *
   etag: "659c79df-2015"
   expires: Thu, 18 Jan 2024 13:02:30 GMT
   cache-control: max-age=600
   x-proxy-cache: MISS
   x-github-request-id: 230E:9C925:489B90:4B6274:65A91F0D
   accept-ranges: bytes
   date: Fri, 19 Jan 2024 17:31:57 GMT
   via: 1.1 varnish
   age: 0
   x-served-by: cache-nrt-rjtf7700058-NRT
   x-cache: HIT
   x-cache-hits: 1
   x-timer: S1705685517.200122,VS0,VE210
   vary: Accept-Encoding
   x-fastly-request-id: cf775ba7f805b8f6b7e163a0c768946d2e04879a
   content-length: 8213
   ```

9. 使用 `|` 和 `>` ，将 `semester` 文件输出的最后更改日期信息，写入主目录下的 `last-modified.txt` 的文件中
   ```shell
   # ./semester | grep last-modified > ~/last-modified.txt
   ```

10. 写一段命令来从 `/sys` 中获取笔记本的电量信息，或者台式机 CPU 的温度。注意：macOS 并没有 sysfs，所以 Mac 用户可以跳过这一题。
    本人使用的是阿里云服务器，没有传感器模块，所以无法查看相关信息

