# 0

集中式版本控制系统(CVS,Subversion):中央服务器为绝对核心。
分布式版本控制系统:不存在中央服务器的概念，所有开发者都拥有自己的版本库，地位上对等。
在具体实践中，git也存在服务器版本库(用途划分):

*   项目版本库:blessed repository 由"官方"创建并发行的版本
*   共享版本库:shared repository 开发团队人员之间的文件交换。
*   工作流版本库:workflow repository 存放项目工作流中的特定版本，例如审核通过后的状态。
*   派生版本库:fork repository 用于从开发主线分离出某部分内容。例如:用于实验的版本。

分布式相对于集中式的优点:性能。

## 本质
版本库的本质是一个高效的数据存储结构，由以下部分组成:

* 文件 blob
* 目录 tree
* 版本 commit 

以上都称为"对象"，每个对象都会被计算成一个十六进制的散列值。每个散列值作为一个对象的引用。一个"提交对象"的散列值就是版本号
散列值算法的优点:

* 相同的对象内容只需要存储一次。
* 散列值是离线产生。如果发生碰撞，说明文件内容相同，碰撞无影响。
* 高效同步:从一个版本库向另一个版本库传递时，只需要传递目标库中不存在的对象即可。用散列值可以很快判断对象是否存在。
* 数据完整性:再次计算文件内容的散列值，即可判断数据是否完整或发生变化
* 自动重命名检测:如果一个文件被重命名，那么由于它的文件内容散列值没有发生变化,git使用移动命令即可。 

# 入门

帮助
git help commit
q退出
b 上一页
space 下一页
d/u 翻半页(down/up)
/pattern 匹配模式 ,n/N 下一个/上一个

查看git版本 
git --version

git config 会列出可食用的选项。
git config --global 列出global配置
git config --system system
git config --local 列出该项目的配置
git config --local user.email  列出用户邮箱
git config --local user.email "user@group.com" 设置邮箱
git config --global color.ui true 打开输出内容颜色
删除配置
git config --unset --local user.email 
配置优先级:System \< gloabl \< local (范围越大优先级越低)

创建版本库:

1. 选择一个文件目录作为项目目录,包含版本库的目录也称为`工作区`.
2. git init。这时版本库就创建成功了

`git init`:调用该命令后，会在当前目录生成`.git`，它是一个目录，包含了git项目相关信息。

请注意git是分布式版本库，不要误以为创建版本库只能使用git clone命令从某个服务器上拉取。即使存在"中央服务器"，它同本地版本库的地位是对等的。

提交:

1. git add foo.txt bar.txt  或者 git add *
2. git commit --message 'desc' 或者 git commit -m 'desc'

使用git status可以查看当前没有提交的修改。
使用 git diff foo.txt 可以查看修改。该命令在cmd中并不直观。推荐使用图形工具kdiff3

git diff #工作区与暂存区的差异
git diff --cached #暂存区与HEAD(版本库最新提交)的差异，也可以使用--stage
git diff HEAD #工作区与版本库的差异
 gitk #该命令会调用gitk工具，可以图形化的形式查看提交记录
使用git rm foo.txt 可以删除某文件。它使用的不多，因为linux/mac系统命令rm同样能删除文件。git rm的区别仅仅是它同时删除了git的记录。

git log 
列出项目的提交历史.git log -10 列出最近的10条。
git log --stat #列出详细信息

git rev-parse master #列出master对应的提交id，它是一个底层命令
git cat-file -t commitid # 列出commitid对应的对象类型

使用40位散列值(sha1哈希算法)的好处是，文件内容和id对应是全球唯一的。

HEAD #版本库最新提交
master #master分支最新提交
HEAD^ #HEAD的父提交，注意是父提交，不是上一次提交
HEAD^^ #HEAD的父提交的父提交
HEAD~1 == HEAD^
HEAD~2 == HEAD^^

## 协作

### 克隆
`git clone /projects/project-a /projects/project-a-clone`
其中原版本库可以是一个远程版本库地址。也可以是远程版本库地址的别名(例如origin)。
`git remote add origin 'github_repo_url'`

从原版本库获取新的提交:
git pull 
直接使用该命令会触发两个操作:pull新的提交，然后合并merge.它默认的是当前克隆体版本库和原版本库。

它等同于:
git pull <原版本库> <当前版本库>
完整参数:git pull <版本库a> <版本库b>
提交传递方向:a->b

git push <版本库a> <版本库b>
提交传递方向:b->a

将提交发送给其他版本库。
可以在本地创建一个不带工作区的版本库，用于充当开发者互相传递提交的汇聚点。
git clone --bare 'github_repo_url'
在本地会生成一个文件夹:project-a-bare.git，它没有工作区，所以里面看不到内容。

在实践中
origin:一般指源版本库
master:本地版本库的master分支。这是创建版本库时默认的分支


# 提交

git提交的不是单个文件，而是整个项目。一次提交，该项目中每个文件都被存入了版本库中。git的软件版本也是指整个项目的版本，而不是某个文件。
git保留文件的访问权限，但不保留文件的修改时间，这是为了ide工具能够正确rebuild，设想某文件的提交时间为2020-01-01，检出时间为2020-01-02，ide上一次build时间是2020-01-02，ide可能会认为该文件已经build过，造成错误的build。所以git会将检出的文件修改时间都用当前时间代替。

git fsck 可以查看版本的完整性，原理即是比较散列值和重新计算的文件散列值

git checkout fweqasd123123 
或
git checkout HEAD  #非常危险，同时覆盖暂存区和工作区。
可以检出指定的提交版本。

每个克隆体都是项目的历史，同一个项目存在多部不同历史。



git log 可以查看提交历史。
git log -n  3 #只显示最后三条提交
git log --oneline #每条提交记录在一排
git log --stat #只显示提交的统计信息

git log --stat #显示被修改的文件
git log --dirstat #显示被修改的目录
git log --shortstat 显示修改文件的数量
git log --graph 显示各提交的关系

## 多次提交

提交流程：
工作区 -> 暂存区 -> 版本库

git status 可以列出`工作区`中发生的修改，及这些修改的提交状态。
git status --short #简略地显示修改状态

将`暂存区`中未提交的修改移除:
git reset HEAD bbb.txt
git reset HEAD aaa.txt bbb.txt src/test/
重置整个暂存区
git reset HEAD

`HEAD`
HEAD不是指当前分支的别名,而是一个引用，指向版本库最新提交。可以在`.git/HEAD`中查看文件内容
利用git add xx.txt 可以只提交部分修改的文件，它有严重的弊端：
版本库当中的项目版本从未在`工作区`存在过。


git reset HEAD bbb.txt存在的问题?
如果在add以后bbb.txt又有新的修改，重置以后，第一次add的内容就不存在于三个区:工作区，暂存区，版本库.
虽然提交内容即使丢失也通常不是一个问题，因为当前工作区的文件可能是最正确的版本。

git reset --hard HEAD^  
将工作区和暂存区都重置为当前HEAD的父提交。可能会造成修改丢失。使用该命令后，历史也将改变(git log)，要想回到之前的提交，要使用git reflog.

git reflog记录了分支变更。也可以在.git/logs/heads/branch-name 中查看 branch-name分支的head变更历史.显然这个文件是追加式的，最新的提交应当在文件末尾。
如果用 
git reflog show master | head -5
输出最新的5次变更历史，它的输出结果最新的提交在最上面。

7e2368a master@{0}: commit: xx
9bf9285 master@{1}: pull origin master: Fast-forward
111be01 master@{2}: commit: xx
622f4ed master@{3}: pull origin master: Fast-forward
04bb39b master@{4}: pull origin master: Merge made by the 'recursive' strategy.

可以使用master@{1}代表master分支的上一次head指向。

`git reset`:危险的命令，它会改变分支的指向。
两种用法:
1. paths:如果使用的是路径，是使用指定提交id的文件替换掉暂存区的文件
2. commit-id/refs:如果使用的是引用或提交id,是用指定的提交状态，对暂存区或工作区进行重置(由参数决定) 。 

git reset HEAD ./src/Init.java #使用HEAD中的 Init.java 重置暂存区的该文件，作用效果就是撤销(git add ./src/Init.java)

git reset --hard commitid 
该命令参数会执行3步:
1. 版本库提交状态更改为commitid
2. 暂存区文件内容与commitid保持一致
3. 工作区文件内容与commitid保持一致

git reset --soft commitid # 只执行第1步
git reset --mixed commitid #默认选项。执行1，2步，不改变工作区

举例:
git reset HEAD  # HEAD一般是最新提交，第1步是相同状态，所以它的效果:移除了暂存区的add内容。
git reset #同上，不过最好不要使用
git reset HEAD -- filename #将filename移除暂存区
git reset -- filename #将filename移除暂存区

git reset --soft HEAD^ #将版本库指向HEAD的父提交，它可用于回退版本库，然后重新提交。
git reset --hard origin/master 利用origin(一般是远程版本库)的master分支覆盖 本地的 工作区&暂存区&版本库




`暂存区`
它的作用：
1. 列出下次提交的文件清单
2. 存储修改的内容
暂存区存储的是git add 以后的版本
git diff --staged 可以查看暂存区与版本库之间的不同
git diff 查看暂存区与工作区之间的不同

`git checkout`
注意与`git reset`的区别:reset是改变某分支(master)的指向，而HEAD文件内容不变，仍是指向master,HEAD指向的提交变化是因为master引起的。
而checkout是直接改变HEAD指向的引用，是HEAD自身发生变化，而不是引用发生变化。HEAD总是指向某个分支的头部。
git reset:改变分支的指向,不改变HEAD。
git checkout:改变HEAD头指针指向,不改变分支。

如果使用 git checkout commitid ，而恰好当前分支的最新提交是commitid0，那么此时HEAD就没有指向分支头部，违反常态，git会认为当前处于"分离头指针"状态:detached HEAD。
这种状态下可以测试、提交，而不影响任何分支，原因是该状态不处于任何分支。如果git checkout master切换回master分支后，分离头指针状态下的修改都将丢失。

git reflog -1  # 输出HEAD的变更记录

git chceckout :危险命令。它会重写工作区。

可以看出git reset主要是为了重置暂存区，而git checkout重置工作区。

git checkout 用法:
主要有三种

1. filename:重写(暂存区或工作区)文件
2. git branch branchname:更改HEAD指向
3. git branch -b newbranch:创建新的分支


1.  git checkout branch-name # 变更HEAD指向，切换到branch-name分支
2.  git checkout # 显示工作区&暂存区 同HEAD的差异
3.  git checkout HEAD #同2,命令较难理解，不要使用。HEAD本身肯定可以追溯到一个提交，checkout到一个提交会如何？
4.  git checkout HEAD -- filename:用暂存区的filename覆盖工作区的filename.即丢掉git add filename以后的修改
5.  git checkout branchname -- filename:用branchname分支的filename覆盖*工作区和暂存区*的filaname。但HEAD不改变。
6.  git checkout . # 用暂存区覆盖工作区
7.  git checkout -- . #同6



#### 使用.gitignore忽略非版本控制文件
通常有以下几类:
1. 自动生成的文件
2. 临时文件
3. 不希望提交的文件

在项目根目录下新建.gitignore文件。
还可以使用* 和 & 通配符。
test/  # 忽略所有路径包含`test/`的文件
/test/ # 忽略/test/下的文件

*.bak # 忽略所有.bak结尾的文件
!j.bak # 不忽略j.bak

注意：
它只能影响当前还未交由git来管理的文件，如果文件已经存在于版本库，那么该方式失效。
如果想忽略一个已经被版本化的文件，可以使用update-index 命令的--assume-unchanged来做到这一点


### git stash
该命令将`工作区`和`暂存区`的修改保存在储藏栈stash stack中。
git stash pop # 弹出(恢复)最新的储藏
git stash pop stash@{0} # 弹出指定的储藏
git stash list # 列出储藏栈的内容

该命令可以用来处理冲突。git stash以后，工作区将与版本库保持一致，可以进行git pull。因此处理冲突的流程为:
1. git pull # 发现冲突，无法pull
2. git stash # 储藏修改，工作区置为当前版本库版本
3. git pull # 捡取最新提交
4. git stash pop # 弹出储藏的修改

第4步显然还包含自动merge，如果无法自动merge会如何呢?
无法自动merge仍然会merge，不过会提示有冲突，并在冲突文件中有如下内容:
`c
 <<<<< Updated upstream 
 ....a1
 =====
 ...a2
 stashed changes
`
其中a1就是pull下来的内容，====和stashed changes之间的内容a2就是本地修改的内容.


# 版本库

`porcelain command`瓷质命令 ，提供给用户使用的较为直观实用的命令。如git log等
`plumbing`管道，底层命令。瓷质命令是由底层命令构建的。

## 核心
git的核心是一个对象数据库，它可以存储文本或二进制数据。
git hash-object -w xxx.txt #将文件xxx.txt写入对象数据，返回值是40字符，即对象的id
git cat-file -p id #  查看id的内容

## 对象类型
对象有两种:blob ，tree。(本质上没区别，都是数据库的一条记录)
blob(联通)对象按字节存储。
tree对象存储目录信息，顾名思义，tree对象存储是一种树结构。
举例:
project-a/			# 它应该也有一条记录
	jdbc.properties   # blob id0  文件内容
	src/		# tree id1 
		a.java  # blob ida 文件内容
		b.java  # blob idb 文件内容
		test/   # tree idtest 目录内容

其中id1的内容应包含以下信息(即能找到该树的下一层叶子或树枝):
blob ida
blob idb
tree idtest

注意:
1. 相同数据只存储一次。因此两个不同路径的相同内容的文件，共享一条记录。
2. 如果修改a.java ，它的新id变为ida0，那么必须新增一条src/的信息，src变化又引起其他变化...
要处理这种变化，显然更合适的方式提交时一次性提交全部版本文件。

压缩相似内容：
gc命令可以删除不再接受任何分支头访问的提交。

# 分支

为什么需要分支？
分支最重要的用途:可以保证版本的稳定性。
当 版本提交不能依次进行了就需要分支。例如生产环境的版本有bug需要修复，直接修改最新版本是不合适的(会将其他无关的开发代码发布上去)。


A-B-C(发行版)-D(开发中)
发行版发现bug，创建分支修复bug
A-B-C-D
    |
    E
 
 
`版本库中总是存一个唯一的当前活跃分支。`

## 分支相关命令

git branch #列出所有分支 带星号的是当前分支
`C
git branch

a-branch
* master
b-branch
`
master可以认为指的是第一个存在的分支，通常也是“主分支”。
它是指向该分支的指针。




git checkout a-branch #切换到分支a-branch

git branch a-branch #为当前提交创建分支

git branch b-branch c123123123 #为任意提交创建分支

git branch b-branch a-branch #为任意分支创建分支


git checkout -b a-branch #创建分支并切换到新分支，这个命令我不推荐使用，干了太多事，容易出错。

它也是一个可能会造成修改丢失的命令

请注意，如果工作区存在未提交修改，git checkout a-branch命令将拒绝执行。因为强行切换会丢失所有修改。这也可以看出
，git checkout a-branch命令会切换分支的原理是:版本库指向新分支，工作区和暂存区都被重置成版本库分支的最新提交的状态。
同解决冲突的方法类似：

1. 先提交修改。
2. 强行切换，丢掉修改。git checkout --force a-branch
3. git stash

`重置分支指针`
git reset --hard commit-id 
这个命令会将当前分支的指针指向commit-id所在的活动分支，并且暂存区和工作区都被设置成提交commit-id的状态。
所以它也是一个可能会造成修改丢失的命令。

`删除分支`
git branch -d b-branch
使用这个命令的前提是先chekcout至其他分支。
删除的原理是删除了分支名称和分支之间的关联，分支是仍然保留的。
所以恢复分支很容易
git branch  a-branch commit-id
如果不确定分支的提交散列值，可以使用git reflog命令，它记录的HEAD(活动分支)的记录

gc 可以清理版本库，移除所有不属于当前分支的提交对象


# 合并分支

git merge a-branch
将a-branch合并到当前分支。merge会产生一次新的提交。git隐藏了复杂的合并过程。
合并git merge使用算法：递归算法，octopus(多分支)

自动合并能够处理同一文件但不同行的冲突，如果冲突发生在一行，则无法自动合并。
合并的原理是根据文件的提交历史，找到两个分支冲突文件的共同祖先，然后对比三个文件，决定冲突解决方案。
虽然有冲突，但git merge仍然会对文件进行改动。结果通常如下：

...
`<<<<<<<<HEAD`
valid

`<<<<<<<a-branch`
invalid
...

使用git status可以查看git merge的结果。如果不想要这个结果，可以撤销合并：
git reset --merge

合并冲突有两种：

1.  编辑冲突:2名开发者修改同一行代码
2.  内容冲突:2名开发者修改不同行的代码
其中第2种冲突git不一定能正确发现，它有时候能正常合并，但代码的业务逻辑无法保证正确。

git merge a-branch
默认是快进合并，即它会直接将b-branch的指针指向最新的提交。
这样做的缺点是 b-branch的提交历史记录丢失：它变成了a-branch的历史。
使用git merge --no-off a-branch 
可以强制产生一次新的提交，这样b-branch的历史得以保留:它是直接从某个历史提交合并到最新的提交的。

合并冲突的解决
多分支冲突应当逐个击破。

git log a..b
显示来自于b分支但不属于a分支的提交。
git log MERGE_HEAD..HEAD
显示当前分支(HEAD)的提交
git log HEAD..MERGE_HEAD
显示别人所做的事情(MERGE_HEAD是合并时产生的指向另一分支的指针)

合并冲突的原则应当是尽量谨慎，冲突问题已经超出git的能力范围，必须是由人工确认/代码测试。

举例：将本地分支推送至远程分支。

1. 切换分支:git checkout a-branch
2. git push
fatal: The current branch a-branch has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin a-branch
3. git push --set-upstream origin a-branch
将分支推送到远程版本库并且将远程版本库的分支切换为a-branch

# 通过变基净化历史

git rebase 通常有以下2个应用场景:

1. 如果不小心在错误的分支上执行了一次提交，可以利用git rebase将提交转移到正确的分支上
2. 整合分叉过多的提交历史为平滑线性历史。

它的副作用:它会改变历史。包含合并操作，可能带来冲突

## 原理：复制提交
变基操作的工作原理:git会让想要移动的提交序列在目标分支按照相同的顺序再现一遍。由于分支的不同，这些提交的散列值通常都是"新提交散列值"。
所以它可能带来冲突。

`git rebase --abort`:取消rebase命令

举例：
A-B       #master
|
C-D       #a-branch

>a-branch是活动分支

`git reabse master` :

1. 确认提交:确认在活动分支 且 不在目标上的提交,即C D.
2. 确认提交位置:确认目标分支(master)上要执行变基操作的地方，即B
3. 复制提交:将CD复制到目标分支,创建新的提交C'D'。
4. 重置活动分支:将活动分支指向提交D'所在的分支。

A-B
  |
  C'-D'         #master


注意:CD仍在版本库中，只是不再可见，但可以利用散列值直接访问。执行GC之后，将被清理掉。

通常使用git pull --rebase命令。

`git pull`= git fetch + git merge FETCH_HEAD
`git pull --rebase` = git fetch + git rebase FETCH_HEAD

区别是:git merge 会将2个分支汇聚成一个新提交。

`遇到冲突怎么办?`
1. 简单粗暴的方法:放弃变基，git rebase --abort
2. 处理冲突:处理冲突文件之后依次执行
    git add conflict.file
    git rebase --continue


如果已经将提交push至远程版本库，那么不该对该提交执行rebase操作了，因为其他开发者可能在此提交上有新的提交。

# cherry-pick
复制提交的另外一种方式
`git cherry-pick commit-id`
它会创建一次新的提交。
它可用于将一次bug修复的提交传递至多个版本。
它可在决定删除某分支之前，移动有用的提交。
该操作的对版本库的修改较小。
git命令的缺陷是 相当混乱，加个参数命令作用即天差地别，同时也缺少对危险命令的确认机制。


# 版本库间的切换


## 克隆

git help clone 可查看命令详情

执行clone的场景：
1.在中央服务器上执行:往往是为了对项目进行重大改变；修改重大bug；备份。
2.在开发者的服务器上执行clone:每个开发者都要一个克隆版本才能展开工作

clone版本库
git clone file://filepath
git clone ssh://sshaddr
git clone url

别名:
git remote add repo_name file://filepath
删除别名:
git remote rm repo_name
git remote --verbose

获取新的提交:
git fetch repo_name #获取所有提交
也可以指定分支名称，只获取该分支的提交





