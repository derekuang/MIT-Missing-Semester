# Data Wrangling



## 一、数据整理的概念

我们经常需要将获取到的数据（文件的内容、命令输出的结果等等）转换成另一种格式的数据，然后打印或保存起来。

主要来说，将数据根据我们的需求进行匹配，对数据重新进行整理输出。如`grep`指令、`ls`指令的各种选项（详细信息、排序、可读形式等等）其实都是在做数据整理。



## 二、正则表达式

- 工具网站

  - 正则表达式可视化网站

    [regex-vis](https://regex-vis.com/)

  - 正则表达式测试网站

    [regexr](https://regexr-cn.com/)

- 常见模式

  `.` 除换行符外的任意单个字符

  `*` 匹配前面的字符零次或多次

  `+` 匹配前面的字符一次或多次

  `?`匹配前面的字符零次或一次

  `[abc]` 匹配 `a`, `b` 和 `c` 中的任意一个

  `(RX1|RX2)` 任何能够匹配`RX1` 或 `RX2`的结果

  `^` 行首

  `$` 行尾

  注：`*`和`+` 默认情况下是贪婪模式，即尽可能匹配多的文本。可以加一个后缀`?`使其变成非贪婪模式。
  
  `\1...N` 捕获组 被圆括号内匹配到的文本，会被存入一系列以编号区分的捕获组中，捕获组的内容可以在替换字符串时候使用



## 三、awk-另一种编辑器

`awk`其实是一种编程语言，只是刚好非常善于处理文本。

事实上，我们完全可以抛弃 `grep` 和 `sed` ，因为 `awk` 就可以解决所有问题。



## 四、作业

1. 学习一下这篇简短的 [交互式正则表达式教程](https://regexone.com/)。

2. 统计words文件 (`/usr/share/dict/words`) 中包含至少三个`a` 且不以`'s` 结尾的单词个数。

   ```shell
   cat /usr/share/dict/words | tr 'AS' 'as' | \
   grep -E ".*a.*a.*a.*[^s]$" | wc -l
   ```

   这些单词中，出现频率前三的末尾两个字母是什么？ 

   ```shell
   cat /usr/share/dict/words | tr 'AS' 'as' | \
   grep -E --color=never  ".*a.*a.*a.*[^s]$" | \
   sed 's/.*\(..\)$/\1/' | sort | uniq -c | sort -nk1,1 | tail -n3
   ```

   `sed`的 `y`命令，或者 `tr` 程序也许可以帮你解决大小写的问题。共存在多少种词尾两字母组合？

   ```shell
   cat /usr/share/dict/words | tr 'AS' 'as' | \
   grep -E --color=never  ".*a.*a.*a.*[^s]$" | \
   sed 's/.*\(..\)$/\1/' | sort | uniq -c | sort -nk1,1 | wc -l
   ```

   还有一个很 有挑战性的问题：哪个组合从未出现过？

   `words.sh`

   ```bash
   #!/usr/bin/env bash
   
   # 获取任意两个字母的组合
   for i in {a..z}
   do
       for j in {a..z}
       do
           echo "$i$j"
       done
   done
   ```

   ```shell
   diff --unchanged-group-format='' <(./words.sh) <(cat /usr/share/dict/words | tr 'AS' 'as' | grep -E --color=never  ".*a.*a.*a.*[^s]$" | sed 's/.*\(..\)$/\1/' | sort | uniq -c | awk '{print $2}')
   ```

3. 进行原地替换听上去很有诱惑力，例如： `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`。但是这并不是一个明智的做法，为什么呢？还是说只有 `sed`是这样的? 查看 `man sed` 来完成这个问题

   在使用重定向输出>时，被覆盖的文件会先被清空，如果这时指令发生错误将造成数据丢失。

4. 找出您最近十次开机的开机时间平均数、中位数和最长时间。在Linux上需要用到 `journalctl` ，而在 macOS 上使用 `log show`。找到每次起到开始和结束时的时间戳。在Linux上类似这样操作：

   ```
   Logs begin at ...
   ```

   和

   ```
   systemd[577]: Startup finished in ...
   ```

   先获取最近十次开机日志

   ```bash
   journalctl | grep "systemd\[1\]: Startup finished in" | tail -n10 > startup.log
   ```

   计算各个时间值

   ```bash
   #!/usr/bin/env bash
   
   timelist=$(sed 's/.*= \([0-9]\+\.[0-9]\+\)s\.$/\1/' startup.log)
   len=$(echo "$timelist" | wc -l)
   
   # 平均数
   sum=0
   for t in $timelist
   do
       sum=$(awk "BEGIN {print $sum+$t}")
   done
   echo avg time: "$(awk "BEGIN {print $sum/$len}")"
   
   # 中位数
   echo median time: "$(echo "$timelist" | sort -n | awk '{ a[i++]=$1; } END { print a[int(i/2)]; }')"
   
   # 最长时间
   echo max time: "$(echo "$timelist" | sort -n | tail -1)"   
   ```

5. 查看之前三次重启启动信息中不同的部分(参见 `journalctl`的`-b` 选项)。将这一任务分为几个步骤，首先获取之前三次启动的启动日志，也许获取启动日志的命令就有合适的选项可以帮助您提取前三次启动的日志，亦或者您可以使用`sed '0,/STRING/d'` 来删除`STRING`匹配到的字符串前面的全部内容。然后，过滤掉每次都不相同的部分，例如时间戳。下一步，重复记录输入行并对其计数(可以使用`uniq` )。最后，删除所有出现过3次的内容（因为这些内容是三次启动日志中的重复部分）。

6. 在网上找一个类似 [这个](https://stats.wikimedia.org/EN/TablesWikipediaZZ.htm) 或者[这个](https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1)的数据集。或者从[这里](https://www.springboard.com/blog/free-public-data-sets-data-science-project/)找一些。使用 `curl` 获取数据集并提取其中两列数据，如果您想要获取的是HTML数据，那么[`pup`](https://github.com/EricChiang/pup)可能会更有帮助。对于JSON类型的数据，可以试试[`jq`](https://stedolan.github.io/jq/)。请使用一条指令来找出其中一列的最大值和最小值，用另外一条指令计算两列之间差的总和。