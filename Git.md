# Git基本使用

## 1. Git的安装与配置

首先是安装：

有空再说

## 2. 代码版本库

然后是版本库的配置，也就是配置repository。我们可以简单理解为一个目录，这个目录下的文件都通过Git进行管理。所以第一步就是创建一个版本库

```shell
#第一步：创建一个目录作为未来的仓库
mkdir learngit#这个就是文件夹名称
cd lerngit
pwd
/Users/name/Desktop/code #返回的路径
#第二部：初始化仓库
git init #在想要建立仓库的目录下
Initialized empty Git repository in /Users/name/Desktop/code/.git/
```

这样仓库就创建好了，我们可以通过ls -ah来查看里面的文件，这样就会显示出一个.git文件，这个文件不要动，修改了它可能仓库就损坏了。

## 3. Git基本使用之add 和 commit

首先需要明确，所有的版本控制系统，其实只能跟总文本文件的改动，其他文件是无法知道具体改动是什么样子的。而需要注意的是，Microsoft的word是二进制格式储存的，也就是说我们无法通过git知道word的改动。

其次，我们常用有GBK编码等，建议使用UTF-8，因为所有语言使用同一种编码，既没有冲突，也被所有平台接受。

最搞笑的是，千万不要使用Windows自带的**记事本**编辑任何文本文件。原因是Microsoft开发记事本的团队使用了一个非常弱智的行为来保存UTF-8编码的文件，他们自作聪明地在每个文件开头添加了0xefbbbf（十六进制）的字符，你会遇到很多不可思议的问题，比如，网页第一行可能会显示一个“?”，明明正确的程序一编译就报语法错误，等等，都是由记事本的弱智行为带来的。建议你下载[Visual Studio Code](https://code.visualstudio.com/)代替记事本，不但功能强大，而且免费！

接下来，我们可以通过一个例子来展示git的基本使用。首先随便弄一个文本, 但是这这个文本一定要在创建的仓库目录下。

```shell
echo "Git is a version control system.
Git is free software." > readme.txt

git add readme.txt
#执行完这个命令不需要返回值，相当于我们将readme.txt文件添加至我们的仓库中

git commit -m "wrote a readme file"
#执行这个命令就是把文件提交到仓库。 -m参数表示带上后面的message
#生产中必须加上，不然大家都不写，都不知道现在这个版本是干啥的。
#如果想不加可以加入参数 --allow-empty-message
```

至于为什么需要git add 和git commit，是因为commit一次可以提交多个文件，我们可以达到多次，然后一次commit。

## 4. Git基本使用之diff 和 status

在上一章中，我们已经成功添加并提交了一个txt文件，接下来我们就可以开始尝试提交修改并进行版本控制。

首先修改一下之前的文件，这里可以通过shell中的open打开然后修改，也可以双击，我比较懒，直接vim修改的。我将文本修改为了

```
Git is a distributed version control system.
```

其实就是加入了一个词：distributed， 然后可以通过git status 进行状态查看。

```shell
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

也可以通过git diff来查看做了什么修改

```shell
diff --git a/readme.txt b/readme.txt
index 46d49bf..dd90832 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is distributed a version control system.
 Git is free software.
(END)
```

其中可以明显看出我们做了哪些修改。这个命令可以帮我们回忆一下我们之前做了什么。

总的来说就是git status告诉我们是否有文件被修改过，git diff则是告诉我们做了什么修改。

## 5. Git基本使用之版本回溯（git reset）

上一章我们修改了文件，并且提交了。现在我们就要展示如何做到版本控制，也就是回溯版本。

首先我们再复习一次上一章的操作。首先将readme.txt修改为：

```shell
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

然后通过git add 和git commit提交

```shell
[main 5586c59] append GPL
 1 file changed, 1 insertion(+), 1 deletion(-)
```

现在，我们一共就是三个版本了:

```shell
#版本1： this is readme file
Git is a version control system.
Git is free software.

#版本2： add distributed
Git is a distributed version control system.
Git is free software.

#版本1： append GPL
Git is a version control system.
Git is free software distributed under GPL.
```

然后我们就可以通过git log 来查看我们一共做过什么（忽略第一次提交多出来的符号，属于是打错了）

![截屏2023-05-23 17.01.06](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-23 17.01.06.png)

如果感觉内容太多，可以通过 git log --pretty=oneline![截屏2023-05-23 17.03.34](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-23 17.03.34.png)

可以看到，前面是提交的ID，后面是我们写的message。

好了，接下来就是惊心动魄的时候了，我们即将穿梭时空，回溯到之前的样子。

```shell
➜  code git:(main) git reset --hard HEAD^
HEAD is now at 22bce41 add distributed
```

可以看到，我们运行之后，版本好回到了22bce的版本。接下来我们double check 一下我们的readme.txt。（至于--head是干什么的，我们一会在说）

```shell
➜  code git:(main) cat readme.txt
Git is distributed a version control system.
Git is free software.
```

非常nice，我们回到了第二个版本。但是这个时候我们使用git log会发现，之前第三版的修改看不到了。

![截屏2023-05-23 17.11.58](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-23 17.11.58.png)

这下麻烦了，我们如果想回到GPL版本可怎么办。实际上根本不慌，我们可以通过之前看到的版本号，重新回到最新版本。

```shell
➜  code git:(main) git reset --hard 5586
HEAD is now at 5586c59 append GPL
➜  code git:(main) cat readme.txt
Git is distributed a version control system.
Git is free software distributed under GPL.
```

如果我们回溯版本之后，关电脑了，第二天后悔了，但是这时候shell中的纪录早就无了，怎么办。我们可以通过git reflog来查看我们执行的所有的命令。

总结一下，git log 中保存了每个版本的ID，我们可以通规git reset --hard [版本号]来进行回溯。回溯之后log中不再保存版本ID，这个时候如果又想回到最新版本，可以通过git reflog查看所有执行过的命令，其中就有版本号。

## 6. 工作区和暂存区

Git和其他版本控制系统的一个不同之处就是Git存在工作区和暂存区的概念。

工作区指的就是我们创建仓库的目录，而版本库指的是仓库目录下有一个隐藏的.git文件（可以通过ls  -ah查看）。

其中，版本库存放了很多东西，就包括了成为stage的暂存区，并且git会自动为我们创建一个master分支，以及一个指针指向master。就像下面展示的一样。![截屏2023-05-24 10.52.37](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-24 10.52.37.png)

分支和head的概念不用着急，听不懂可以先跳过去，我们之后会提到。

还记得之前的提交操作吗，我们都是分两步进行：

第一步是将想要提交的文件通过git add命令添加进去，其实这个就是将文件提交到暂存区。

第二步是通过git commit，将暂存区的文件全部提交到当前分支。

这里需要理解一下的是，由于我们目前没有创建任何分支，当前分支就是git自动创建的master分支，所以我们就会提交到master分支上。

说了这么多，该试一下。

首先我们再修改一下倒霉的readme.txt， 然后再增加一个文件。

```shell
#修改readme
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage
```

第二步，增加一个文件LICENSE

```shell
➜  code git:(main) ✗ echo "hello,world" > LICENSE
➜  code git:(main) ✗ ls
LICENSE    readme.txt
➜  code git:(main) ✗ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   readme.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	LICENSE

no changes added to commit (use "git add" and/or "git commit -a")
```

可以看到的是，readme又被修改过，并且多了一个Untraced files，也就是我们新加入的文件。

现在让我们将两个文件都放入暂存区，

 

```shell
➜  code git:(main) ✗ git add readme.txt
➜  code git:(main) ✗ git add LICENSE
➜  code git:(main) ✗ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   LICENSE
	modified:   readme.txt
```

然后提交就可以了。

```shell
➜  code git:(main) ✗ git commit
[main 4ac1526] "Understand how stage works"
 2 files changed, 2 insertions(+)
 create mode 100644 LICENSE
```

在上面的过程中，git暂存区的变换应该是这样子的:

当我们修改了文件，创建了新文件，并且git add之后，暂存区（stage）是下面这样子。![截屏2023-05-24 11.36.14](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-24 11.36.14.png)

commit之后，暂存区就清空了。

![截屏2023-05-24 11.36.32](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-24 11.36.32.png)

## 7. Git基本使用之修改管理

现在我们已经了解了工作区，暂存区以及master的概念。接下来就来学习一下为什么说Git比其他版本控制系统优秀。其主要原因是Git跟踪并管理的的是修改，并非是文件。

我自己做了个小实验，不修改任何东西的情况下进行git add是不会有任何东西提交到暂存区的。

另一方面，我们也可以验证Git追踪的是修改：

流程为： 修改文件 -》Git add -》再次修改-》Git commit

我们会发现第二次修改没有提交，可见Git记录的是修改。

### 接下里的部分是非常重要的部分，也就是撤销修改

原文写的很有意思，我就直接借鉴了。

自然，我们是不可能犯错的。只不过现在是凌晨两点，你正在赶工一份工作报告，然后你在readme.txt中添加了一句

```shell
$ cat readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
My stupid boss still prefers SVN.
```

在你准备提交前，一杯咖啡起了作用，你猛然发现stupid boss可能会让你丢掉这个月的奖金。

那么很好，错误发现的很及时，很容易就可以纠正他。首当其冲就是删除最后一行，手动把文件恢复到上一个版本。

```shell
➜  code git:(main) ✗ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

这里可以看到readme.txt已经被我们修改了，但是并没有放到暂存区，所以我们可以直接通过git restore readme.txt 或者git checkout -- readme.txt将其还原到之前。

其中git checkout -- readme.txt的意思是讲readme.txt文件在工作区的修改全部撤销。这里分两种情况：

1. 首先是修改之后没有提交到暂存区，这样修改之后就和版本库一致。
2. 如果已经执行过git add，则会回到暂存区的版本。

总结一下就是，如果暂存区有这个文件，那就回到上一次git add前，否则回到上一次git commit。



上一个问题终于解决了，现在是凌晨3点，现在你不但写了一写胡话，还git add了。

```shell
➜  code git:(main) ✗ cat readme.txt
Git is distributed a version control system.
Git is free software distributed under GPL.
Git has a mutable index called stage.
Three O clock.

➜  code git:(main) ✗ git add readme.txt

➜  code git:(main) ✗ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   readme.txt
```

庆幸的是在commit之前，你又发现了这个问题。幸亏修改只是添加到了暂存区，没有提交。你赶紧通过git restore --staged file进行回溯

```shell
#下面这一行等同于 git restore -- staged file
➜  code git:(main) ✗ git restore -S readme.txt
➜  code git:(main) ✗ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")

➜  code git:(main) ✗ git checkout -- readme.txt
➜  code git:(main) cat readme.txt
Git is distributed a version control system.
Git is free software distributed under GPL.
Git has a mutable index called stage.
```

我们可以看到，执行resore之后文件还没有改，但是已经从暂存区退回到工作区了。

然后就可以用我们解决第一个问题是用到的git checkout -- file 来进行进一步回退了。

现在，假设你不但改错了东西，还从暂存区提交到了版本库，怎么办呢？还记得[版本回退](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)一节吗？可以回退到上一个版本。不过，这是有条件的，就是你还没有把自己的本地版本库推送到远程。还记得Git是分布式版本控制系统吗？我们后面会讲到远程版本库，一旦你把`stupid boss`提交推送到远程版本库，你就真的惨了…

最后再做个小总结：

https://blog.csdn.net/Sweet_19BaBa/article/details/111950384

1. 如果还没有git add就准备回退可以通过git checkout -- file 或者 git restore file撤回之前的修改
2. 如果add了还没commit，可以通过git restore --staged file 或者git restore -S file进行回退，他会讲暂存区的文件回退到工作区，然后执行第一步就可以撤销我们的修改。
3. 如果commit了，可以通过 git reset head回到最新的版本
4. 如果提交到远程仓库了，推荐连夜跑路。

## 8. Git 基本使用之删除文件

前面说过了撤销了修改，那如果你想要删除一个文件怎么办。

首先我们先创建一个新的文件text.txt，并且提交一下

```shell
➜  code git:(dev) echo "hello" > text.txt
➜  code git:(dev) ✗ ls
LICENSE    readme.txt text.txt
➜  code git:(dev) ✗ git add text.txt
➜  code git:(dev) ✗ git commit -m "add a text"
[dev 8ba9451] add a text
 1 file changed, 1 insertion(+)
 create mode 100644 text.txt
```

可以看到现在仓库里已经有了这个文件，那现在我们想把它删掉。首先就可以直接在本地删了。

```shell
➜  code git:(dev) rm text.txt
➜  code git:(dev) ✗ git status
On branch dev
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	deleted:    text.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

可以看到，输入rm之后，git status现实我们删除了一个文件，这个时候就有两个选项了，一个是如果我们是错删了，可以通过git checkout -- file找回。另一个选项就是如果没删错，那就可以通过git rm file git commit直接删除。

这里有个新的理解，就是git checkout -- file其实是拿版本库替换掉工作区的文件，无论是修改还是删除都可以通过这种方式复原。

```shell
#撤回删除操作
➜  code git:(dev) ✗ git checkout -- text.txt
➜  code git:(dev) ls
LICENSE    readme.txt text.txt

➜  code git:(dev) git status
On branch dev
nothing to commit, working tree clean

#重新删除
➜  code git:(dev) rm text.txt
➜  code git:(dev) ✗ git rm text.txt
rm 'text.txt'
➜  code git:(dev) ✗ git status
On branch dev
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	deleted:    text.txt

#彻底删除
➜  code git:(dev) ✗ git commit -m "remove text,txt"
[dev 6484d0e] remove text,txt
 1 file changed, 1 deletion(-)
 delete mode 100644 text.txt
 
➜  code git:(dev) git status
On branch dev
nothing to commit, working tree clean
➜  code git:(dev) ls
LICENSE    readme.txt
```



## 9. Git基本使用之分支

这一章的内容就像对复杂一些了，但是也非常有意思。Git的核心我觉得就应该是这个分支间的切换以及合并了。这张内容较多，我会分成几个子章节。

### 9.1 创建和合并分支

在之前版本回退的章节，我们已经了解了Git会把所有的提交串成一条线，类似于一个链表，其实这个链表就是master分支。而到目前为止，我们还没有创建任何分支，也就是说只有一条时间线。

而我们之前命令中用过的HEAD，当时没有详细介绍，只是说了我们可以通过git reset --hard HEAD^ 来回溯到上一个版本。现在我们要重新认识一下HEAD。

HEAD严格来说不是指向提交，而是指向master，而master才是指向提交的。所以事实上，HEAD指向的就是当前的分支。

最开始的时候，我们只有一个分支，当我们提交的时候，master会指向新的提交，而HEAD再指向master，每次提交，master都会向前走一步。

![截屏2023-05-25 09.59.20](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-25 09.59.20.png)

那当我们创建新的分支的时候，比如创建了一个dev分支，Git就会新建一个指针叫dev，指向master相同的提交，再把head指向dev，表示目前是dev分支。

![截屏2023-05-25 10.05.47](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-25 10.05.47.png)

所以，Git创建一个分支是非常非常快的，因为其实只是增加了一个指针并修改了HEAD指针的指向，工作区的文件并不会发生任何变化。

但是从现在开始，我们如果在dev上进行commit就不会在移动master了。

那么问题来了，如果现在我们dev更新完了，想要合并到master上，我们怎么办呢？

最简单的办法当然是直接把master指向dev支线上最新的提交就可以了。![截屏2023-05-25 10.08.59](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-25 10.08.59.png)

可以发现的是，合并也很快，因为也是指针的改动。然后dev分支就没用了，可以直接删除。这样就又只剩下一个master线了。只能说，牛牛🐮。我们甚至不知道那些提交是通过分支完成的。

![截屏2023-05-25 10.12.05](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-25 10.12.05.png)

OK，现在我们了解了基本原理，就可以看看具体操作了。

首先我们需要创建dev分支，然后切换过去。

```
# 两种办法
# 第一种
➜  kuaishou-build-tools git:(dev) git checkout -b dev
# 第二种
➜  kuaishou-build-tools git:(master) git branch
➜  kuaishou-build-tools git:(master) git checkout dev
Switched to branch 'dev'
```

现在如果我们通过git branch查看分支状态就能看到，我们现在已经处于dev分支上了，那么现在就在dev分支上做一些提交吧。比如加上一句。

```shell
➜  code git:(dev) ✗ git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
➜  code git:(dev) ✗ git add readme.txt
➜  code git:(dev) ✗ git commit -m "branch test"
[dev 762eb83] branch test
 1 file changed, 1 insertion(+)
```

现在我们回到master，看看会发生什么。

```shell
#我这里分支名称叫main，这个地方因人而异
➜  code git:(dev) git checkout main 
Switched to branch 'main'
➜  code git:(main) cat readme.txt
Git is distributed a version control system.
Git is free software distributed under GPL.
Git has a mutable index called stage.
➜  code git:(main)
```

绝了，我们的修改无了，这是因为我们的提交是在dev分支上，而master分支看不到。

![截屏2023-05-25 10.23.29](/Users/yuximeng/Library/Application Support/typora-user-images/截屏2023-05-25 10.23.29.png)

现在，我们就需要dev上的修改合并到master上了。

```shell
➜  code git:(main) git merge dev
Updating 4ac1526..762eb83
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
➜  code git:(main) cat readme.txt
Git is distributed a version control system.
Git is free software distributed under GPL.
Git has a mutable index called stage.
Creating a new branch is quick.
```

现在我们的head，master，dev指向的就是同一个节点了。

另一方面，merge时候的信息告诉我们，这是一个Fast-forward合并，也就是快进模式。这个模式指的就是直接把master指针指向dev直线当前的提交。

接下来dev就没用了，我们干掉他。

```shell
➜  code git:(main) git branch -d dev
Deleted branch dev (was 762eb83).
```

在查看分支就会发现，只剩下master了。

还有一个小地方需要注意，就是我们既可以通过git checkout <branch>来切换分支，也可以通过git switch branch来进行切换。而git checkout -- <file> 使用用来抛弃工作区修改的，这就容易让人很迷惑。

接下来就是一些关于switch的使用

```shell
#创建并切换分支
➜  code git:(main) git switch -c <branch>
#切换分支
➜  code git:(main) git switch <branch>
```

okkkkkk，接下来又是总结环节。总结环节太重要了，因为之前说的东西虽然都不难，但是都是新的知识。而我们是人类，我们记忆的能力更多是通过重复的使用来记住的。所以，总结可以让我们快速回忆我们刚学的知识。

总结：

```shell
# 查看分支
git branch

# 切换分支
git switch <branch>
git checkout <branch>

#创建分支
git branch -c <branch>

#创建并切换分支
git switch -c <branch>
git checkout -b <branch>

#删除分支
git branch -d branch

#合并分支
git merge <branch>
```

### 9.2 解决冲突

世界就是充满冲突的，合并分支也是。我们在和并分支的时候有可能出现两个人修改同一个内容，这种情况Git就不知道应该抛弃哪个了，就会出现冲突。

比如我们可以先转被一个新的分支，







## 多人协作

git push 

推送分支，如果远程仓库没有则新建分支

git pull

事实上是git fetch 和 git merge的融合，相当于同步所有远程分支到本地，并且只对当前分支进行merge

git remote 

可以用来查看远程仓库地址

Git rebase

其实没有很复杂，就是把你的分支的提交重新提交到当前分支最新的提交的后面。和get merge的区别就是merge会多一次合并提交并且保留分支路线，所以merge看起来会臃肿一些。