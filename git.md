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

配置

git config 会列出可食用的选项。
git config --global 列出global配置
git config --system system
git config --local 列出该项目的配置
git config --local user.email  列出用户邮箱
git config --local user.email "user@group.com" 设置邮箱

创建版本库:

1. 选择一个文件目录作为项目目录,包含版本库的目录也称为`工作区`.
2. git init。这时版本库就创建成功了

请注意git是分布式版本库，不要误以为创建版本库只能使用git clone命令从某个服务器上拉取。即使存在"中央服务器"，它同本地版本库的地位是对等的。

提交:

1. git add foo.txt bar.txt  或者 git add *
2. git commit --message 'desc' 或者 git commit -m 'desc'

使用git status可以查看当前没有提交的修改。
使用 git diff foo.txt 可以查看修改。该命令在cmd中并不直观。推荐使用图形工具kdiff3
使用git rm foo.txt 可以删除某文件。它使用的不多，因为linux/mac系统命令rm同样能删除文件。git rm的区别仅仅是它同时删除了git的记录。

git log 
列出项目的提交历史.git log -10 列出最近的10条。

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
git checkout fweq
可以检出指定的提交版本。

每个克隆体都是项目的历史，同一个项目存在多部不同历史。



