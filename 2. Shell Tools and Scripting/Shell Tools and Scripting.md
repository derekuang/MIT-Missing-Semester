# Shell Tools and Scripting



## 一、Shell脚本

- 什么是Shell脚本

  虽然我们可以在Shell中输入命令并执行，但这往往只适用于一些简单的场景。当我们需要一连串的操作并使用条件和循环这样的控制流，以及对这些操作进行保存来反复使用时，我们就需要编写Shell脚本代码。

- Shell脚本和Shell的关系

  不同的Shell有自己的一套脚本语言，像是变量、控制流和语法等。Shell的脚本语言专门用来对Shell的操作进行优化，所以我们可以在Shell脚本中直接在Shell中可以使用的命令和程序文件连接等，相对于其它脚本语言，这些操作都是原生的。

- 基本语法（以Bash为例）

  - 赋值 `foo=bar`
    注意`=`两边不能有空格，如果是`foo = bar`，Bash解释器会理解成要调用`foo`程序，参数是`=`和`bar`，自然是走不通的。总之，空格在Shell脚本中起分割参数的作用。

  - 取值 `$foo`

  - 字符串

    - `"string"` 双引号定义的字符串会进行变量扩展和转义

    - `'string'` 单引号定义的字符串为原义字符串，不会被变量扩展和转义

    - ```shell
      # echo "$foo"
      bar
      # echo '$foo'
      $foo
      ```

  - 短路运算符 `&&` `||`

  - 函数

    ```shell
    f () {
    	语句1
    	语句2
    	...
    }
    ```

    - 参数
      `$0` - 脚本名
      `$1` 到 `$9` - 脚本的参数。 `$1` 是第一个参数，依此类推。
      `$@` - 所有参数
      `$#` - 参数个数
      `$?` - 前一个命令的返回值
      `$$` - 当前脚本的进程识别码。脚本/命令执行后的一个状态，用于脚本/命令间交流执行状态。0表示正常执行，其它非0值表示有错误发生。
      `!!` - 完整的上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。
      `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式 shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。
    - 其它特殊符号参考资料
      [Special Characters](https://tldp.org/LDP/abs/html/special-chars.html)

  - 命令替换（command substitution）`$(CMD)`
    `for file in $(ls)` for循环遍历ls命令返回值

  - 进程替换（process substitution） `<(CMD)` 
    执行CMD并将结果输出到临时文件，把`<(CMD)`替换成文件名
    `diff <(ls foo) <(bar)` 对比文件夹foo和bar中文件区别

  - 通配符
    `?` 匹配一个字符
    `*` 匹配任意个字符

  - 花括号 `{}` 对拥有公共子串的字符串进行自动展开
    `mv *{.py,.sh} folder` = `mv *.py *.sh folder` 

  - shebang `#!` 向内核解释用什么解释器来运行脚本
    `#!/bin/bash` 使用/bin下的bash
    `#!/usr/local/bin/python` 使用指定路径下的python解释器
    `#!/usr/bin/env python` 通过env命令寻找`PATH`环境变量下的python解释器，用env使得脚本可移植性更好



## 二、Shell工具

- `shellcheck` 定位sh/bash脚本中的错误
- `tldr(too long don't read)` 获取命令的简单用法说明，不像`man`指令的手册内容过于详实
- 查找文件
  - `find` 查找文件或目录 
  - `fd` `find`的替代方案，更简单、快速、用户友好
  - `locate` 最主要是通过索引数据库的方法比`find`更快，但数据库并非实时，而是定时更新，可以通过`updatedb`更新

- 查找文本
  - `grep` 对输入文本进行匹配查找
  - `rg` `ripgrep` `grep`的替代方案，速度快，符合直觉

- 查找Shell命令
  - `history` 查找Shell中输入的历史命令
  - `Ctrl+R` 对命令历史记录回溯搜索
  - `fzf` 模糊搜索（可以把`Ctrl+R`绑定到`fzf`）

- 文件夹导航
  - `fasd` `autojump`  查找最常用或最近使用的文件或目录
  - `tree` 以树状形式展示目录结构
  - `nnn` 更完整的终端文件管理器

- shell命令解释网站
  - [exlainshell](https://explainshell.com)




## 三、作业

1. 阅读 [`man ls`](https://man7.org/linux/man-pages/man1/ls.1.html) ，然后使用`ls` 命令进行如下操作：

   - 所有文件（包括隐藏文件）
   - 文件打印以人类可以理解的格式输出 (例如，使用454M 而不是 454279954)
   - 文件以最近访问顺序排序
   - 以彩色文本显示输出结果

   ```shell
   # ls -laht --color=always
   ```

2. 编写两个bash函数  `marco` 和 `polo` 执行下面的操作。 每当你执行 `marco` 时，当前的工作目录应当以某种形式保存，当执行 `polo` 时，无论现在处在什么目录下，都应当 `cd` 回到当时执行 `marco` 的目录。 为了方便debug，你可以把代码写在单独的文件 `marco.sh` 中，并通过 `source marco.sh`命令，（重新）加载函数。
   ```bash
   #!/usr/bin/env bash
   
   path=/tmp/marco
   
   marco() {
       echo "$PWD" > $path
   }
   
   polo() {
       cd "$(cat $path)" || exit
       rm $path
   }
   ```

   ***参考答案：***

   ```bash
   #!usr/bin/env bash
   
   marco() {
   	export MARCO="$PWD"
   }
   
   polo() {
   	cd "$MARCO"
   }
   ```

3. 假设您有一个命令，它很少出错。因此为了在出错时能够对其进行调试，需要花费大量的时间重现错误并捕获输出。 编写一段bash脚本，运行如下的脚本直到它出错，将它的标准输出和标准错误流记录到文件，并在最后输出所有内容。 加分项：报告脚本在失败前共运行了多少次。
   ```bash
    #!/usr/bin/env bash
   
    n=$(( RANDOM % 100 ))
   
    if [[ n -eq 42 ]]; then
       echo "Something went wrong"
       >&2 echo "The error was using magic numbers"
       exit 1
    fi
   
    echo "Everything went according to plan"
   ```

   ```bash
   #!/usr/bin/env bash
   
   output=./buggy.log
   echo > $output # 清空文件
   
   i=0
   while true
   do
       ./buggy.sh &>> $output
       if(( $? != 0 )); then
           break
       fi
       ((i++))
   done
   
   echo "===================="
   echo "Fail after $i times"
   ```

4. 本节课我们讲解的 `find` 命令中的 `-exec` 参数非常强大，它可以对我们查找的文件进行操作。但是，如果我们要对所有文件进行操作呢？例如创建一个zip压缩文件？我们已经知道，命令行可以从参数或标准输入接受输入。在用管道连接命令时，我们将标准输出和标准输入连接起来，但是有些命令，例如`tar` 则需要从参数接受输入。这里我们可以使用[`xargs`](https://man7.org/linux/man-pages/man1/xargs.1.html) 命令，它可以使用标准输入中的内容作为参数。 例如 `ls | xargs rm` 会删除当前目录中的所有文件。

   您的任务是编写一个命令，它可以递归地查找文件夹中所有的HTML文件，并将它们压缩成zip文件。注意，即使文件名中包含空格，您的命令也应该能够正确执行（提示：查看 `xargs`的参数`-d`，译注：MacOS 上的 `xargs`没有`-d`，[查看这个issue](https://github.com/missing-semester/missing-semester/issues/93)）

   ```bash
   #!/usr/bin/env bash
   
   DIR=$1
   
   # 递归获取DIR文件夹中所有HTML文件
   find "$DIR" -type f \( -name "*.htm" -o -name "*.html" \) -exec printf "'%s'\n" {} \; \
   | xargs zip html.zip
   ```

   ***参考答案：***

   ```bash
   find . -type f -name "*.html" | xargs -d '\n'  tar -cvzf html.zip
   ```

5. （进阶）编写一个命令或脚本递归的查找文件夹中最近使用的文件。更通用的做法，你可以按照最近的使用时间列出文件吗？

   ```bash
   #!/usr/bin/env bash
   
   find . -type f -exec ls -lt {} + | head -1
   ```

   ***参考答案：***

   ```bash
   find . -type f -mmin -60 -print0 | xargs -0 ls -lt | head -1
   ```

   
