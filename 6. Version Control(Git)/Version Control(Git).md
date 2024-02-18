# Version Control(Git)



## 一、Git的数据模型

- 快照

  在Git中，文件被称为Blob对象，目录被称为树，在树中将名字映射到Blob或子树。

  而快照则是最顶层的树（Git仓库根目录），Git对这个快照进行跟踪。

- 历史记录建模：关联快照

  在Git中，历史记录是一个由快照构成的有向无环图，每个快照都有一个或多个parent，而这些快照则被称为commit。

  如下ASCII码简图，每一个`o`表示一次commit：

  ```
  o <-- o <-- o <-- o <---- o
              ^            /
               \          v
                --- o <-- o
  ```

- Git历史模型简介伪代码

  ```
  // 文件就是一组数据
  type blob = array<byte>
  
  // 一个包含文件和目录的目录
  type tree = map<string, tree | blob>
  
  // 每个提交都包含一个父辈，元数据和顶层树
  type commit = struct {
      parent: array<commit>
      author: string
      message: string
      snapshot: tree
  }
  ```

- Git对象

  Git的对象分为blob、树和commit：

  ```
  type object = blob | tree | commit
  ```

  所有的对象会基于它们的SHA-1哈希进行寻址，当对象被引用时，也是保存它们的哈希值作为引用：

  ```
  objects = map<string, object>
  
  def store(object):
      id = sha1(object)
      objects[id] = object
  
  def load(id):
      return objects[id]
  ```

- 引用

  现状：所有的快照都可以通过它们的哈希值来标记，但这并不方便，一串40位的十六进制串可读性很差。

  解决：给这些哈希值赋予人类可读的名字，也就是引用。引用是指向commit的指针。

  ```
  references = map<string, string>
  
  def update_reference(name, id):
      references[name] = id
  
  def read_reference(name):
      return references[name]
  
  def load_reference(name_or_id):
      if name_or_id in references:
          return load(references[name_or_id])
      else:
          return load(name_or_id)
  ```

  特殊的引用值：

  - `master` 指向主分支的最后一次提交
  - `HEAD` 当前工作所在位置（用来给新的commit设置parent）

- 仓库

  现在，可以对仓库进行粗略的定义：对象和引用。

  在硬盘上，Git仅存储对象和引用。所有的git命令都围绕对提交树进行操作，如增加对象、增加或删除引用。

  

## 二、暂存区

考虑如下场景：

1. 你开发了两个独立的特性，修改了多个文件，希望分别提交两个特性，即每次提交一部分修改的文件；
2. 调试代码时，希望只提交和修复bug相关的代码，而丢弃调试时添加的打印语句。

Git处理这些场景的方法是使用一种叫“暂存区（staging area）”的机制，允许指定下次提交中包括的改动。

对于以上场景，可以使用`git add -p <file>`进行交互式暂存处理，可以对修改的文件添加一部分到暂存区，而保持工作区的文件不变。



## 三、Git的命令行接口

- 参考`Pro Git`
- Git提交信息
  - 如何编写 [良好的提交信息](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
  - 为何要 [编写良好的提交信息](https://chris.beams.io/posts/git-commit/)



## 四、Git高级操作

- 略



## 五、杂项

- 图形用户界面：Git 的 [图形用户界面客户端](https://git-scm.com/downloads/guis)
- Shell集成：[zsh](https://github.com/olivierverdier/zsh-git-prompt), [bash](https://github.com/magicmonty/bash-git-prompt)
- 编辑器集成：略
- 工作流：进行大型项目时的一些惯例 ( 有[很多](https://nvie.com/posts/a-successful-git-branching-model/) [不同的](https://www.endoflineblog.com/gitflow-considered-harmful) [处理方法](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow))
- Github：在 GitHub 中您需要使用一个被称作[拉取请求（pull request）](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)的方法来向其他项目贡献代码
- 其它Git提供商： [GitLab](https://about.gitlab.com/), [BitBucket](https://bitbucket.org/)



# 六、资源

- [Pro Git](https://git-scm.com/book/en/v2) ，**强烈推荐**！学习前五章的内容可以教会您流畅使用 Git 的绝大多数技巧，因为您已经理解了 Git 的数据模型。后面的章节提供了很多有趣的高级主题。（[Pro Git 中文版](https://git-scm.com/book/zh/v2)）；
- [Oh Shit, Git!?!](https://ohshitgit.com/) ，简短的介绍了如何从 Git 错误中恢复；
- [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/) ，简短的介绍了 Git 的数据模型，与本文相比包含较少量的伪代码以及大量的精美图片；
- [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/)详细的介绍了 Git 的实现细节，而不仅仅局限于数据模型。好奇的同学可以看看；
- [How to explain git in simple words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)；
- [Learn Git Branching](https://learngitbranching.js.org/) 通过基于浏览器的游戏来学习 Git ；



## 七、作业

1. 如果您之前从来没有用过 Git，推荐您阅读 [Pro Git](https://git-scm.com/book/en/v2) 的前几章，或者完成像 [Learn Git Branching](https://learngitbranching.js.org/)这样的教程。重点关注 Git 命令和数据模型相关内容；

2. Fork 

   本课程网站的仓库

   1. 将版本历史可视化并进行探索
   2. 是谁最后修改了 `README.md`文件？（提示：使用 `git log` 命令并添加合适的参数）
   3. 最后一次修改`_config.yml` 文件中 `collections:` 行时的提交信息是什么？（提示：使用 `git blame` 和 `git show`）

3. 使用 Git 时的一个常见错误是提交本不应该由 Git 管理的大文件，或是将含有敏感信息的文件提交给 Git 。尝试向仓库中添加一个文件并添加提交信息，然后将其从历史中删除 ( [这篇文章也许会有帮助](https://help.github.com/articles/removing-sensitive-data-from-a-repository/))；

4. 从 GitHub 上克隆某个仓库，修改一些文件。当您使用 `git stash` 会发生什么？当您执行 `git log --all --oneline` 时会显示什么？通过 `git stash pop` 命令来撤销 `git stash` 操作，什么时候会用到这一技巧？

5. 与其他的命令行工具一样，Git 也提供了一个名为 `~/.gitconfig` 配置文件 (或 dotfile)。请在 `~/.gitconfig` 中创建一个别名，使您在运行 `git graph` 时，您可以得到 `git log --all --graph --decorate --oneline` 的输出结果；

6. 您可以通过执行 `git config --global core.excludesfile ~/.gitignore_global` 在 `~/.gitignore_global` 中创建全局忽略规则。配置您的全局 gitignore 文件来自动忽略系统或编辑器的临时文件，例如 `.DS_Store`；

7. 克隆 [本课程网站的仓库](https://github.com/missing-semester-cn/missing-semester-cn.github.io.git)，找找有没有错别字或其他可以改进的地方，在 GitHub 上发起拉取请求（Pull Request）；

