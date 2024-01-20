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

- shellcheck 定位sh/bash脚本中的错误



## 三、作业

