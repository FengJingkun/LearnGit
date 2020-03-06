## QuickStart

### 什么是版本库？

版本库可以理解为一个目录，这个目录里的所有文件通过Git进行管理，每个文件的增、删、改都能被Git追踪，以便任何时候都能追踪历史，或在未来进行还原。具体地，Git通过版本库所在目录下的一个名为**.git**的隐藏目录来对进行管理。



### 创建版本库

创建版本库主要分为两个步骤：

1. 指定一个目录（文件夹），目录可以是一个新建文件夹，也可以是一个已存在的文件夹；
2. 在指定目录下执行**git init**命令。

```bash
$ mkdir learnGit
$ cd learnGit
$ git init
```

**git init**命令将当前目录变成Git可以管理的仓库，即版本库。该命令执行后，会在当前目录下生成一个**.git**隐藏目录，Git通过这个目录来对版本库进行跟踪和管理。



### 添加文件到版本库

为了保证文件能被Git正确追踪，文件**一定**要放在版本库所在的目录下。添加一个文件到版本库主要分为两个步骤：

1. 使用**git add**命令，将文件**添加**到**暂存区**；
2. 使用**git commit**命令，将文件**提交**到**版本库**。

```bash
$ touch readme.txt
$ git add readme.txt
$ git commit readme.txt -m "wrote a readme file"
```

其中，**-m**参数输入的是本次提交的说明。

> 为什么需要add，commit两步？
>
> commit可以一次性提交多个文件，所以可以多次使用add将多个文件添加到仓库后，再使用一次commit将新添加的所有文件提交到仓库。



### 检查文件当前状态

**git status**命令用于查看版本库中的文件处于什么状态。

执行**git status**后若看到如下输出，说明当前版本库中所有已被Git追踪的文件在上次提交之后都未被更改过，同时也表明，当前目录下没有新增任何处于未追踪状态的新文件。

```bash
$ git status
On branch master nothing to commit, working directory clean 
```

特别地，上述输出还显示了当前所在分支，**master**即当前所在的分支名。

若现在对之前提交的readme.txt文件进行了修改，执行**git status**后会显示：

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt
```

上述信息表明，readme.txt文件被修改过了，但是并未提交到版本库中。若要提交到版本库中，使用**git add和git commit**即可。



### 查看文件的修改内容

**git status**可以指明被修改的文件，但不能查看具体的修改内容。

若要查看文件修改后的内容变化，可以使用**git diff**命令。

```bash
$ git diff readme.txt
diff --git a/readme.txt b/readme.txt
index f7249b8..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system
+Git is a distributed version control system.
 Git is free software.
```



### 版本回退

在多次commit后，若要将当前的版本库回退到过去提交的某一个版本时，首先可以使用**git log**命令查看由近到远的提交日志，特别地，使用**git log --pretty=oneline**显示的日志信息更为简洁：

```bash
$ git log --pretty=oneline
073b7e4442f3456f4373f0cbb16c4c6fda93b2ab (HEAD -> master) append GPL
5a40a6b7c0dca0ec651c7011c876a6deee675fea add distributed
6d3cd818b73254e830910d5cfd73839d2834b4c4 wrote a readme file
```

其中，诸如073b...之类的字符是**commit id（版本号）**。特别地，Git的版本号是由哈希函数SHA1计算出的一个十六进制数字，这么做的目的是防止多人协作中使用简单id可能会造成的版本号冲突。

在Git中，**HEAD**指针指向当前版本，即最新提交的073b...，上一个版本用**HEAD^或HEAD~1**表示，再往前一个版本用**HEAD^^或HEAD~2**表示，以此类推即可。

若把版本号回退到“wrote a readme file”版本，需要使用**git reset**命令：

```bash
$ git reset --hard HEAD~2
HEAD is now at 6d3cd81 wrote a readme file
```

--hard参数指定回退的版本号，若要再次回到append GPL版本，则指定具体的版本号即可。特别地，版本号只需指定开头的前几个字母，Git会进行自动寻找。

```bash
$ git reset --hard 073b
```

若显示之前版本号的窗口已关闭，那么需要使用**git reflog**命令，该命令用来记录执行过的历史指令。

```bash
$ git reflog
6d3cd81 HEAD@{1}: reset: moving to HEAD~2
073b7e4 (HEAD -> master) HEAD@{2}: commit: append GPL
5a40a6b HEAD@{3}: commit: add distributed
6d3cd81 HEAD@{4}: commit (initial): wrote a readme file
```

根据所打印的信息即可查到append GPL版本的版本号。

> **关于HEAD**
>
> HEAD是Git内部实现的一个指针，它始终指向当前版本，当回退版本的时候，实际上只是改变了HEAD指针的指向，使其指向了另一个版本，同时更新了当前版本库目录下的文件。



### 管理修改

#### 工作区，暂存区，版本库

- **工作区（Working Directory）**就是版本库所在的目录，比如learnGit文件夹，但不包括**.git**隐藏文件夹；
- **版本库（Repository）**即**.git**隐藏目录，它存放了分支master、指向master的HEAD指针、日志和**暂存区**等内容；
- **暂存区（Stage）**对应**.git**目录下的**index文件**，它保存了下次将要commit到版本库中的文件信息。

> **git add**命令实际上是将**文件的修改**添加到暂存区，Git因此才能对文件进行追踪；
>
> **git commit**命令则是将暂存区的所有内容提交到当前分支。

**Git跟踪并管理的是修改，而非文件**。

在工作区对文件进行修改后，若不将修改放入暂存区（即不使用add命令），那么这次修改不会被Git所追踪。

> 比如，对readme文件进行修改并通过**git add**添加到暂存区后，再次对readme文件进行修改，然后直接使用**git commit**提交到版本库中，那么版本库中的readme文件是第一次修改过的内容，第二次修改则未被提交。



### 撤销修改

在撤销对文件的修改时，可分为以下两种情况：

1. 修改了一个文件，但是未使用**git add**命令，修改后的文件只存在于工作区中；
2. 修改了一个文件，并使用了**git add**命令将文件添加到了暂存区中。

针对情况1，使用**git status**命令查看当前状态，显示如下：

```bash
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   QuickStart.md

no changes added to commit (use "git add" and/or "git commit -a")
```

由打印信息可以看出，我们可以使用**git restore**命令撤销这次修改。此时，QuickStart.md文件的内容与版本库中的内容保持一致。

```bash
$ git restore QuickStart.md
```

针对情况2，使用**git status**查看当前状态，显示信息如下：

```bash
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   QuickStart.md
```

因此，同样可以使用**git restore**加上参数**staged**将提交到暂存区的文件撤回。随后，再次使用**git status**可以看到暂存区是干净的，工作区存在修改，**此时的状态与情况1一致。**

```bash
$ git restore --staged QuickStart.md
```



### 删除文件

在Git中，**删除也是一种修改操作。**

对于已提交到版本库中的文件，使用如下操作进行删除：

1. 首先手动删除待删除的文件；

   ```bash
   $ rm test.txt
   ```

2. 使用**git rm**命令从版本库中将文件删掉；

   ```bash
   $ git rm test.txt
   ```

3. 提交本次修改到版本库。

   ```bash
   $ git commit -m "delete test.txt"
   ```

> 上述操作并不是直接删除了版本库中的某个版本号，而是对删除操作进行了一次记录并在版本库中生成了一个新的版本号，在该版本下没有被删除的文件。若要在版本库中恢复被删除的文件，利用版本回退中用到的**git reset --hard commitID**命令即可。



