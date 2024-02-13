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



## 三、Git的命令行接口



## 四、Git高级操作



## 五、杂项



## 六、作业

