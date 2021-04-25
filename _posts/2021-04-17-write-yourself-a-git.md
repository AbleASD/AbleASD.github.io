---
title: Write yourself a git
categories:
- tutorial
toc: true
---

# Write yourself a git

参考 [Write yourself git](https://wyag.thb.lt/)

> 学习Write yourself git 一方面熟悉git规则, 另一方面学习精炼的代码和一些python库的使用, 记录学习过程中的理解和感悟
***

**实现如下功能：**

* <kbd>add</kbd>() [git man page](https://git-scm.com/docs/git-add)
* <kbd>cat-file</kbd>() [git man page](https://wyag.thb.lt/#cmd-cat-file)
* <kbd>checkout</kbd>() [git man page](https://git-scm.com/docs/git-checkout)
* <kbd>commit</kbd>() [git man page](https://git-scm.com/docs/git-commit)
* <kbd>hash-object</kbd>() [git man page](https://git-scm.com/docs/git-hash-object)
* <kbd>init</kbd>() [git man page](https://git-scm.com/docs/git-init)
* <kbd>log</kbd>() [git man page](https://git-scm.com/docs/git-log)
* <kbd>ls-tree</kbd>() [git man page](https://git-scm.com/docs/git-ls-tree)
* <kbd>merge</kbd>() [git man page](https://git-scm.com/docs/git-merge)
* <kbd>rebase</kbd>() [git man page](https://git-scm.com/docs/git-rebase)
* <kbd>rev-parse</kbd>() [git man page](https://git-scm.com/docs/git-rev-parse)
* <kbd>rm</kbd>() [git man page](https://git-scm.com/docs/git-rm)
* <kbd>show-ref</kbd>() [git man page](https://git-scm.com/docs/git-show-ref)
* <kbd>tag</kbd>() [git man page](https://git-scm.com/docs/git-tag)

***
*note:* 可在任意Unix-like System上运行, 但是Windows上的效果未知.
#### 第一步：
创建两个文件wyag，libwyag.py
``` python
# wyag
#!/usr/bin/env python3
import libwyag
libwyag.main()
```



**libwyag.py依赖如下：**

```python
import argparse  #CLI
import collections  # 用到OrderedDict
import configparser  # 配置文件
import hashlib
import os
import re
import sys
```



***dest="command"***将命令行参数值存储到字段***command***中.

```python
argparser = argparse.ArgumentParser(description="The stupid content tracker")

argsubparsers = argparser.add_subparsers(title="Commands", dest="command")  # 返回为optinal parameters之一
argsubparsers.required = True  # 设置'-f' '--foo' 等可选选项为必选
```
***


#### main方法

	sys.argv[0]存储的是默认的程序名，之后的字段为命令行传入的参数, 如下可以看到***dest="command"***的作用

``` python
def main(argv=sys.argv[1:]):  # sys.argv[0]为默认的程序名
    args = argparser.parse_args(argv)

    if   args.command == "add"         : cmd_add(args)
    elif args.command == "cat-file"    : cmd_cat_file(args)
    elif args.command == "checkout"    : cmd_checkout(args)
    elif args.command == "commit"      : cmd_commit(args)
    elif args.command == "hash-object" : cmd_hash_object(args)
    elif args.command == "init"        : cmd_init(args)
    elif args.command == "log"         : cmd_log(args)
    elif args.command == "ls-tree"     : cmd_ls_tree(args)
    elif args.command == "merge"       : cmd_merge(args)
    elif args.command == "rebase"      : cmd_rebase(args)
    elif args.command == "rev-parse"   : cmd_rev_parse(args)
    elif args.command == "rm"          : cmd_rm(args)
    elif args.command == "show-ref"    : cmd_show_ref(args)
    elif args.command == "tag"         : cmd_tag(args)
```
***




#### GitRepository类

创建repo, 包括git路径, worktree和configuration等文件的配置. 待补充

``` python
class GitRepository(object):
    """A git repository"""

    worktree = None
    gitdir = None
    conf = None

    def __init__(self, path, force=False):
        """

        :param path:.git根目录路径
        :param force: check开关
        """
        self.worktree = path
        self.gitdir = os.path.join(path, ".git")

        if not (force or os.path.isdir(self.gitdir)):
            raise Exception("Not a Git repository %s" % path)

        # Read configuration file in .git/config
        self.conf = configparser.ConfigParser()
        cf = repo_file(self, "config")

        if cf and os.path.exists(cf):
                self.conf.read([cf])
        elif not force:
            raise Exception("Configuration file missing")

        if not force:
            vers = int(self.conf.get("core", "repositoryformatversion"))
            if vers != 0:
                raise Exception("Unsupported repositoryformatversion %s" % vers)
```



该方法返回.git下的文件及文件夹路径

``` python
def repo_path(repo, *path):
    """Compute path under repo's gitdir."""
    return os.path.join(repo.gitdir, *path)
```



判断是否为文件夹(若为文件则抛出异常), 同时若缺失该文件则根据mkdir的值判断是否创建(否则返回None), 返回路径

```python
def repo_dir(repo, *path, mkdir=False):
    """Same as repo_path, but mkdir *path if absent if mkdir."""

    path = repo_path(repo, *path)  # 获取路径

    if os.path.exists(path):  # 如果路径存在
        if (os.path.isdir(path)):  # 如果路径指示文件夹
            return path 
        else:
            raise Exception("Not a directory %s" % path)

    if mkdir:
        os.makedirs(path)
        return path
    else:
        return None
```



```python
def repo_file(repo, *path, mkdir=False):
    """Same as repo_path, but create dirname(*path) if absent.  For
example, repo_file(r, \"refs\", \"remotes\", \"origin\", \"HEAD\") will create
.git/refs/remotes/origin."""

    if repo_dir(repo, *path[:-1], mkdir=mkdir):
        return repo_path(repo, *path)
```



`.git`文件下包含如下内容：

* `.git/objects/` : the object store, which we’ll introduce [in the next section](https://wyag.thb.lt/#objects).
* `.git/refs/` the reference store, which we’ll discuss . It contains two subdirectories, `heads` and `tags`.
* `.git/HEAD`, a reference to the current HEAD (more on that later!)
* `.git/config`, the repository’s configuration file.
* `.git/description`, the repository’s description file.

***



```python
def repo_create(path):
    """Create a new repository at path."""
    """
	:return repo
	"""
    repo = GitRepository(path, True)

    # First, we make sure the path either doesn't exist or is an
    # empty dir.
    
    if os.path.exists(repo.worktree):
        if not os.path.isdir(repo.worktree):
            raise Exception ("%s is not a directory!" % path)
        if os.listdir(repo.worktree):
            raise Exception("%s is not empty!" % path)
    else:  # 若不存在则创建文件夹
        os.makedirs(repo.worktree)

    #  创建.git下的内容
    assert(repo_dir(repo, "branches", mkdir=True))
    assert(repo_dir(repo, "objects", mkdir=True))
    assert(repo_dir(repo, "refs", "tags", mkdir=True))
    assert(repo_dir(repo, "refs", "heads", mkdir=True))

    # .git/description  
    with open(repo_file(repo, "description"), "w") as f:
        f.write("Unnamed repository; edit this file 'description' to name the repository.\n")

    # .git/HEAD
    with open(repo_file(repo, "HEAD"), "w") as f:
        f.write("ref: refs/heads/master\n")
	# 配置文件内容
    with open(repo_file(repo, "config"), "w") as f:
        config = repo_default_config()
        config.write(f)

    return repo
```

config配置文件为仅有`[core]`选项的INI-like文件和三个字段

- `repositoryformatversion = 0`: the version of the gitdir format. 0 means the initial format, 1 the same with extensions. If > 1, git will panic; wyag will only accept 0.
- `filemode = false`: disable tracking of file mode changes in the work tree.

- `bare = false`: indicates that this repository has a worktree. Git supports an optional `worktree` key which indicates the location of the worktree, if not `..`; wyag doesn’t.

利用python的`configparser`库创建：

```python
def repo_default_config():
    ret = configparser.ConfigParser()

    ret.add_section("core")
    ret.set("core", "repositoryformatversion", "0")
    ret.set("core", "filemode", "false")
    ret.set("core", "bare", "false")

    return ret
```



至此完成读取和创建repo的代码,  现在使其能用命令行创建仓库(就像git init)

```python
argsp = argsubparsers.add_parser("init", help="Initialize a new, empty repository.")

argsp.add_argument("path",
                   metavar="directory",
                   nargs="?",
                   default=".",
                   help="Where to create the repository.")

def cmd_init(args):
    repo_create(args.path)
```
***
#### 查找repo
从当前路径开始往上一层依次查找
``` python
def repo_find(path=".",required=True):
    """
    查找repo
    :param path:
    :param required:
    :return:
    """
    path = os.path.relpath(path)  # 获取当前绝对路径

    if os.path.isdir(os.path.join(path, ".git")):  # 如果当前路径存在.git返回
       return GitRepository(path)

    parent = os.path.relpath(os.path.join(path,".."))  # 获取父节点路径

    if parent == path:
        # os.path.join("/", "..") = /
        # 即已达到跟目录
        if required:
            raise Exception("No git directory.")
        else:
            return None
    return repo_find(parent, required)  # 迭代
```
***

### 读写对象:hash-object & cat-object

* `hash-object` 将文件转换为一个git对象
* `cat-object` 将git对象以标准输出格式打印文件
git 对象包括blob, commit, tag和tree四种，其为ASCII码结构如：
**类型** '\x20' **数据长度**'\x00'**数据内容**

gitObject结构类似，因此有父类：
```python
class GitObject(object):

    repo = None

    def __init__(self, repo, data=None):
        self.repo =repo

        if data != None:
            self.deserialize(data)

    def serialize(self):
        """
        this method MUST be implemented by subclasses
        :return:
        """
        raise Exception("Unimplemented!")

    def deserialize(self, data):
        """
        this method MUST be implemented by subclasses
        :return:
        """
        raise Exception("Unimplimented!")
```
#### 读取Object对象
由于Object有四种不同的类型, 根据类型标签我们决定如何解码Object对象：
```python
def object_read(repo, sha):
    """
    Read object object_id from Git repository repo.  Return a
    GitObject whose exact type depends on the object.
    :param repo:
    :param sha:
    :return:
    """
    path = repo_file(repo, "objects", sha[0:2], sha[2:])

    with open(path, 'rb') as f:
        raw = zlib.decompress(f.read())

        x = raw.find(b' ')
        fmt = raw[0:x]  # 格式字段, 即对象类型

        y = raw.find(b'\x00', x)
        size = int(raw[x:y].decode("ascii"))  # 记录有效对象长度
        if size != len(raw) - 1 - y:  # 如果数据长度和记录不同
            raise Exception("Malformed object {0}: bad length".format(sha))

        # 根据数据类型选择构造器
        if   fmt==b'commit' : c = GitCommit
        elif fmt==b'tree'   : c = GitTree
        elif fmt==b'tag'    : c = GitTag
        elif fmt==b'blob'   : c = GitBlob
        else:
            raise Exception("Unknown type %s for object %s".format(fmt.decode("ascii"), sha))
        return c(repo, raw[y + 1:])

#查找对象
def object_find(repo, name, fmt=None, follow=True):
    return name
```
****

#### 写Object对象

```python
def object_write(obj, actually_write=True):
    # 序列化对象
    data = obj.serialize()
    # 添加数据头
    result = obj.fmt + b' ' + str(len(data)).encode() + b'\x00' + data
    # 计算哈希值
    sha = hashlib.sha1(result).hexdigest()

    if actually_write:
        # 计算路径
        path = repo_file(obj.repo, "objects", sha[0:2], sha[2:], mkdir=actually_write)  # repo_file需要确认
        with open(path, 'wb') as f:
            f.write(zlib.compress(result))
    return sha
```
