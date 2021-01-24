# Git笔记

------

主要参考以下两本书:
 
> * 德 René Prei?el  普莱贝尔 Bj?rn Stachmann 斯拉赫曼. Git学习指南 (Chinese Edition) . Kindle 版本.
> * 蒋鑫. Git权威指南 (Chinese Edition) . 机械工业出版社. Kindle 版本.

《Git学习指南》比较浅显，《Git权威指南》比较深入且详细。


## 0 Git基本构成
###  描述
`Git`(读音[git]),是一种分布式版本控制系统，分布式体现在:安装使用Git的用户(机器)本质上都是相同的地位，它没有服务器/客户端的差异。

一个Git项目的外在形式通常是一个文件夹，文件夹的内容可以是项目代码或学习笔记等等。Git项目的"git信息"都在文件夹的`./git/`目录下，忽略该目录，文件夹同普通的系统目录没有任何不同，可见Git是一个`入侵非常少`的工具。

###  项目构成
这里准确地说，是指`项目Git工作信息构成`，Git是一个工具，它不会对项目原本内容产生特定依赖。

Git项目在工作时，有以下三部分:

 * 工作区:workspace,即Git项目当前文件夹，改动工作区内容等同于改动文件夹内容。
 * 暂存区:stage/index,它是Git特有的缓冲区。
 * 版本库:repository,它保存着工作区提交来的各种项目版本。

> 请注意以上三个区均处于项目文件夹下，其中暂存区、版本库信息就存在./git/目录下

###  使用示例

场景:从远程版本库(项目代码服务器)上获取项目。 
步骤:

下载项目:

* 选择一个文件夹,最好和项目同名:path/project-a/,进入该文件夹。
* `git clone https://github.com/xxxx/project-a.git` #克隆(下载)项目

更新(开发者->远程版本库)

* `git add ./xxx/modfiy.txt`   # 将modify.txt修改纳入暂存区
* `git commit -m '修改modify.txt'` # 提交修改至版本库(本地)
* `git push`    # 将修改推送至远程版本库

更新(开发者<-远程版本库)

* `git pull` 




## 1 基础命令

### 1.1 帮助
`git help commit`
操作指令:
`q` 退出
`b` 上一页
`space` 下一页
`d/u` 翻半页(down/up)
`/pattern` 匹配模式 ,n/N 下一个/上一个

### 1.2 配置

`git --version` 查看git版本 

`git config` 会列出可食用的选项。
`git config --global` 列出global配置
`git config --system` system
`git config --local` 列出该项目的配置
`git config --local user.email`  列出用户邮箱
`git config --local user.email user@group.com` 设置邮箱
`git config --global color.ui true` 打开输出内容颜色
删除配置
`git config --unset --local user.email`
配置优先级:System < gloabl < local (范围越大优先级越低)

### 1.3 创建版本库
创建版本库:


1. 选择一个文件目录作为项目目录,包含版本库的目录也称为`工作区`.
2. `git init` 。这时版本库就创建成功了

`git init`:调用该命令后，会在当前目录生成`.git`，它是一个目录，包含了git项目相关信息。
请注意git是分布式版本库，不要误以为创建版本库只能使用git clone命令从某个服务器上拉取。即使存在"中央服务器"，它同本地版本库的地位也是对等的。

### 1.3 提交

1. `git status` 查看当前未提交的修改
2. git add foo.txt bar.txt  或者 git add *
3. git commit --message 'desc' 或者 git commit -m 'desc'


## 2 版本库
### 2.1 种类
集中式版本控制系统(CVS,Subversion):中央服务器为绝对核心。
分布式版本控制系统:不存在中央服务器的概念，所有开发者都拥有自己的版本库，地位上对等。
在具体实践中，git也存在服务器版本库(用途划分):

*   项目版本库:blessed repository 由"官方"创建并发行的版本
*   共享版本库:shared repository 开发团队人员之间的文件交换。
*   工作流版本库:workflow repository 存放项目工作流中的特定版本，例如审核通过后的状态。
*   派生版本库:fork repository 用于从开发主线分离出某部分内容。例如:用于实验的版本。

分布式相对于集中式的优点:性能。

## 2.2 本质

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

------

## 什么是 Markdown

Markdown 是一种方便记忆、书写的纯文本标记语言，用户可以使用这些标记符号以最小的输入代价生成极富表现力的文档：譬如您正在阅读的这份文档。它使用简单的符号标记不同的标题，分割不同的段落，**粗体** 或者 *斜体* 某些文字，更棒的是，它还可以

### 1. 制作一份待办事宜 [Todo 列表](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#13-待办事宜-todo-列表)

- [ ] 支持以 PDF 格式导出文稿
- [ ] 改进 Cmd 渲染算法，使用局部渲染技术提高渲染效率
- [x] 新增 Todo 列表功能
- [x] 修复 LaTex 公式渲染问题
- [x] 新增 LaTex 公式编号功能

### 2. 书写一个质能守恒公式[^LaTeX]

$$E=mc^2$$

### 3. 高亮一段代码[^code]

```python
@requires_authorization
class SomeClass:
    pass

if __name__ == '__main__':
    # A comment
    print 'hello world'
```

### 4. 高效绘制 [流程图](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#7-流程图)

```flow
st=>start: Start
op=>operation: Your Operation
cond=>condition: Yes or No?
e=>end

st->op->cond
cond(yes)->e
cond(no)->op
```

### 5. 高效绘制 [序列图](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#8-序列图)

```seq
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```

### 6. 高效绘制 [甘特图](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#9-甘特图)

```gantt
    title 项目开发流程
    section 项目确定
        需求分析       :a1, 2016-06-22, 3d
        可行性报告     :after a1, 5d
        概念验证       : 5d
    section 项目实施
        概要设计      :2016-07-05  , 5d
        详细设计      :2016-07-08, 10d
        编码          :2016-07-15, 10d
        测试          :2016-07-22, 5d
    section 发布验收
        发布: 2d
        验收: 3d
```

### 7. 绘制表格

| 项目        | 价格   |  数量  |
| --------   | -----:  | :----:  |
| 计算机     | \$1600 |   5     |
| 手机        |   \$12   |   12   |
| 管线        |    \$1    |  234  |

### 8. 更详细语法说明

想要查看更详细的语法说明，可以参考我们准备的 [Cmd Markdown 简明语法手册][1]，进阶用户可以参考 [Cmd Markdown 高阶语法手册][2] 了解更多高级功能。

总而言之，不同于其它 *所见即所得* 的编辑器：你只需使用键盘专注于书写文本内容，就可以生成印刷级的排版格式，省却在键盘和工具栏之间来回切换，调整内容和格式的麻烦。**Markdown 在流畅的书写和印刷级的阅读体验之间找到了平衡。** 目前它已经成为世界上最大的技术分享网站 GitHub 和 技术问答网站 StackOverFlow 的御用书写格式。

---

## 什么是 Cmd Markdown

您可以使用很多工具书写 Markdown，但是 Cmd Markdown 是这个星球上我们已知的、最好的 Markdown 工具——没有之一 ：）因为深信文字的力量，所以我们和你一样，对流畅书写，分享思想和知识，以及阅读体验有极致的追求，我们把对于这些诉求的回应整合在 Cmd Markdown，并且一次，两次，三次，乃至无数次地提升这个工具的体验，最终将它演化成一个 **编辑/发布/阅读** Markdown 的在线平台——您可以在任何地方，任何系统/设备上管理这里的文字。

### 1. 实时同步预览

我们将 Cmd Markdown 的主界面一分为二，左边为**编辑区**，右边为**预览区**，在编辑区的操作会实时地渲染到预览区方便查看最终的版面效果，并且如果你在其中一个区拖动滚动条，我们有一个巧妙的算法把另一个区的滚动条同步到等价的位置，超酷！

### 2. 编辑工具栏

也许您还是一个 Markdown 语法的新手，在您完全熟悉它之前，我们在 **编辑区** 的顶部放置了一个如下图所示的工具栏，您可以使用鼠标在工具栏上调整格式，不过我们仍旧鼓励你使用键盘标记格式，提高书写的流畅度。

![tool-editor](https://www.zybuluo.com/static/img/toolbar-editor.png)

### 3. 编辑模式

完全心无旁骛的方式编辑文字：点击 **编辑工具栏** 最右侧的拉伸按钮或者按下 `Ctrl + M`，将 Cmd Markdown 切换到独立的编辑模式，这是一个极度简洁的写作环境，所有可能会引起分心的元素都已经被挪除，超清爽！

### 4. 实时的云端文稿

为了保障数据安全，Cmd Markdown 会将您每一次击键的内容保存至云端，同时在 **编辑工具栏** 的最右侧提示 `已保存` 的字样。无需担心浏览器崩溃，机器掉电或者地震，海啸——在编辑的过程中随时关闭浏览器或者机器，下一次回到 Cmd Markdown 的时候继续写作。

### 5. 离线模式

在网络环境不稳定的情况下记录文字一样很安全！在您写作的时候，如果电脑突然失去网络连接，Cmd Markdown 会智能切换至离线模式，将您后续键入的文字保存在本地，直到网络恢复再将他们传送至云端，即使在网络恢复前关闭浏览器或者电脑，一样没有问题，等到下次开启 Cmd Markdown 的时候，她会提醒您将离线保存的文字传送至云端。简而言之，我们尽最大的努力保障您文字的安全。

### 6. 管理工具栏

为了便于管理您的文稿，在 **预览区** 的顶部放置了如下所示的 **管理工具栏**：

![tool-manager](https://www.zybuluo.com/static/img/toolbar-manager.jpg)

通过管理工具栏可以：

<i class="icon-share"></i> 发布：将当前的文稿生成固定链接，在网络上发布，分享
<i class="icon-file"></i> 新建：开始撰写一篇新的文稿
<i class="icon-trash"></i> 删除：删除当前的文稿
<i class="icon-cloud"></i> 导出：将当前的文稿转化为 Markdown 文本或者 Html 格式，并导出到本地
<i class="icon-reorder"></i> 列表：所有新增和过往的文稿都可以在这里查看、操作
<i class="icon-pencil"></i> 模式：切换 普通/Vim/Emacs 编辑模式

### 7. 阅读工具栏

![tool-manager](https://www.zybuluo.com/static/img/toolbar-reader.jpg)

通过 **预览区** 右上角的 **阅读工具栏**，可以查看当前文稿的目录并增强阅读体验。

工具栏上的五个图标依次为：

<i class="icon-list"></i> 目录：快速导航当前文稿的目录结构以跳转到感兴趣的段落
<i class="icon-chevron-sign-left"></i> 视图：互换左边编辑区和右边预览区的位置
<i class="icon-adjust"></i> 主题：内置了黑白两种模式的主题，试试 **黑色主题**，超炫！
<i class="icon-desktop"></i> 阅读：心无旁骛的阅读模式提供超一流的阅读体验
<i class="icon-fullscreen"></i> 全屏：简洁，简洁，再简洁，一个完全沉浸式的写作和阅读环境

### 8. 阅读模式

在 **阅读工具栏** 点击 <i class="icon-desktop"></i> 或者按下 `Ctrl+Alt+M` 随即进入独立的阅读模式界面，我们在版面渲染上的每一个细节：字体，字号，行间距，前背景色都倾注了大量的时间，努力提升阅读的体验和品质。

### 9. 标签、分类和搜索

在编辑区任意行首位置输入以下格式的文字可以标签当前文档：

标签： 未分类

标签以后的文稿在【文件列表】（Ctrl+Alt+F）里会按照标签分类，用户可以同时使用键盘或者鼠标浏览查看，或者在【文件列表】的搜索文本框内搜索标题关键字过滤文稿，如下图所示：

![file-list](https://www.zybuluo.com/static/img/file-list.png)

### 10. 文稿发布和分享

在您使用 Cmd Markdown 记录，创作，整理，阅读文稿的同时，我们不仅希望它是一个有力的工具，更希望您的思想和知识通过这个平台，连同优质的阅读体验，将他们分享给有相同志趣的人，进而鼓励更多的人来到这里记录分享他们的思想和知识，尝试点击 <i class="icon-share"></i> (Ctrl+Alt+P) 发布这份文档给好友吧！

------

再一次感谢您花费时间阅读这份欢迎稿，点击 <i class="icon-file"></i> (Ctrl+Alt+N) 开始撰写新的文稿吧！祝您在这里记录、阅读、分享愉快！

作者 [@ghosert][3]     
2016 年 07月 07日    

[^LaTeX]: 支持 **LaTeX** 编辑显示支持，例如：$\sum_{i=1}^n a_i=0$， 访问 [MathJax][4] 参考更多使用方法。

[^code]: 代码高亮功能支持包括 Java, Python, JavaScript 在内的，**四十一**种主流编程语言。

[1]: https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown
[2]: https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#cmd-markdown-高阶语法手册
[3]: http://weibo.com/ghosert
[4]: http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference

