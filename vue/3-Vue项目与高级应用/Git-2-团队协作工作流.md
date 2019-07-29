Git工作流可以理解为团队成员遵守的一种代码管理方案，在Git中有以下几种常见工作流：

* 集中式工作流
* 功能开发工作流
* Gitflow工作流
* Forking工作流


## 1. 集中式工作流
![image](http://note.youdao.com/yws/res/7022/D601BCED1447469FBEA7AF8E2E6F6FBF)
这种工作方式跟svn类似，它只有一个master分支，开发者会先把远程的仓库克隆到本地，之后的修改和提交都在本地操作，直到在某个合适的时间点将本地的代码合入到远程master。这种工作流比较适合小团队，因为小团队可能不会太多的协作和合流的动作。

在开发者提交自己的修改到master前，需要先fetch在master的新增提交，rebase自己的提交于master的最新版本。
这样做的意思是在说，『我要把自己的修改加到别人已经完成的修改之上』。如果本地修改和上游提交有冲突，Git会暂停rebase过程，给你手动解决冲突的机会.

示例：
#### 第一步，
组长在服务器上创建好中央仓库。如果是新项目，你可以初始化一个空仓库；否则你要导入已有的Git或SVN仓库。
中央仓库应该是个裸仓库（bare repository），即没有工作目录（working directory）的仓库。可以用下面的命令创建：

```
ssh user@host    
git init --bare /path/to/repo.git
```

确保写上有效的user（SSH的用户名），host（服务器的域名或IP地址），/path/to/repo.git（你想存放仓库的位置）。
注意，为了表示是一个裸仓库，按照约定加上.git扩展名到仓库名上。


#### 第二步
所有人克隆中央仓库。
各个开发者创建整个项目的本地拷贝。通过git clone命令完成：
```
git clone ssh://user@host/path/to/repo.git
```
基于你后续会持续和克隆的仓库做交互的假设，克隆仓库时Git会自动添加远程别名origin（『父』仓库）。

#### 第三步
成员A开发功能，并在本地使用标准的Git过程开发功能：编辑、暂存（Stage）和提交：
```
git status # 查看本地仓库的修改状态
git add # 暂存文件
git commit # 提交文件-（这些命令生成的是本地提交，以按自己需求反复操作多次，而不用担心中央仓库上有了什么操作。）
```

一旦成员A完成了他的功能开发，会发布他的本地提交到中央仓库中，这样其它团队成员可以看到他的修改。他可以用下面的git push命令：
```
git push origin master
```
（注意，origin是在其克隆仓库时Git创建的远程中央仓库别名，master参数告诉Git推送的分支。）
由于中央仓库自从成员A克隆以来还没有被任何人更新过，所以push操作不会有冲突，成功完成。

然后，成员B完成在本地的功能开发，并欲推送到中央仓库里面。由于，中央仓库已经被成员A进行了修改，如果B直接运行git push origin master，GIt会提示错误，拒绝其提交，以防止B的提交将A已经提交的新代码覆盖。成员B需要先git pull 合并上游的修改到自己本地仓库中（类似svn update）。具体指令：
```
git pull --rebase origin master
```
rebase说明：[点击打开链接](http://gitbook.liuhui998.com/4_2.html)

--rebase 选项告诉Git把B的提交移到同步了中央仓库修改后的master分支的顶部。不使用rebase这个选项，在Pull之前需要先将远程master分支的最新内容同步到本地，然后将自己的修改加进去，再Pull，这样会导致提交历史会以一个多余的『合并提交』结尾。

对于集中式工作流，最好是使用rebase而不是生成一个合并提交。

## 2. 功能开发工作流
![image](http://note.youdao.com/yws/res/7023/D0FF9B5BAD4048FBB77C6C5623D34678)
这种工作流关注功能开发，不直接往master提交代码保证它是稳定并且干净的，而是从master拉取feature分支进行功能开发，团队成员根据分工拉取不同的功能分支来进行不同的功能开发，这样就可以完全隔离开每个人的工作。当功能开发完成后，会向master分支发起Pull Request，只有审核通过的代码才真正允许合入master，这样就加强了团队成员之间的代码交流，也就是我们常说的Code Review。

开发者每次在开始新功能前先创建一个新分支(这个创建的新分支只在开发者本地吗，还是远程也有？--都有), 功能分支应该有个有描述性的名字，这样可以让分支有个清楚且高聚焦的用途。   一旦某个开发完成一个功能，不是立即合并到master，而是push到中央仓库的功能分支上 并 发起一个Pull Request请求去合并修改到master。 

pull requests能为每个分支发起一个讨论，在分支合入正式项目之前，给其它开发者有表示赞同的机会。另外，如果你在功能开发中有问题卡住了，可以开一个pull requests来向同学们征求建议。pull requests让团队成员之间互相评论工作变成非常方便！

在master分支和功能分支之间，Git是没有技术上的区别，所以开发者可以用和集中式工作流中完全一样的方式编辑、暂存和提交修改到功能分支上。

一旦Pull Request被接受了，发布功能要做的就和集中式工作流就很像了，首先，确定本地的master分支和上游的master分支是同步的。然后合并功能分支到本地master分支 并 push已经更新的本地master分支到中央仓库。

### 应用例子介绍：

下面的示例演示了如何把Pull Requests作为Code Review的方式，但注意Pull Requests可以用于很多其它的目的。
小红开始开发一个新功能， 在开始开发功能前，小红需要一个独立的分支。使用下面的命令新建一个分支：
```
git checkout -b marys-feature master
```

这个命令检出一个基于master名为marys-feature的分支，Git的-b选项表示如果分支还不存在则新建分支。这个新分支上，小红按老套路编辑、暂存和提交修改，按需要提交以实现功能：
```
git status

git add <some-file>

git commit
```
然后，小红去吃午饭前，先push 一次，这样可以方便地备份，如果和其它开发协作，也让他们可以看到小红的提交。
```
git push -u origin marys-feature
```
这条命令push marys-feature分支到中央仓库（origin）的marys-feature分支，-u选项设置本地分支去跟踪远程的哪一个分支，设置好跟踪的分支后，后面小红就可以使用git push命令， 省去指定推送分支的参数。

小红吃完午饭回来，完成整个功能的开发，并合并到远程分支。然后，在她的Git GUI客户端中发起Pull Request，请求合并marys-feature到master，团队成员会自动收到通知




## 3. Gitflow工作流
![image](http://note.youdao.com/yws/res/7020/1815017DC7DF40AFBB77E62B43163949)


该种工作流会相对复杂一点，但非常适合用来管理大型项目的发布和维护。贯穿整个开发周期，master和develop分支是一直存在的。

master分支可以被视为稳定的分支，一般不允许直接往master分支提交代码，只允许往这个分支发起merge request，只允许release分支和hotfix分支进行合流。

develop分支是相对稳定的分支，用于日常开发，包括代码优化、功能性开发。

feature分支从develop分支拉取，特性开发会在其上进行，开发完毕合后并到develop分支。

release分支从develop分支拉取，用于回归测试，完成后打tag并合入master和develop。
hotfix分支用于紧急修复上线版本的问题，修复后打tag并合入master和develop。

##### Gitflow 工作流详解:https://my.oschina.net/u/1177694/blog/3020762

##### gitflow 工作流示例：https://blog.csdn.net/wwj_748/article/details/55226044

## 4. Forking工作流
![image](http://note.youdao.com/yws/res/7026/155F71B1076A4A8DB05C7B12DC2F1AA7)

Forking工作流常用于开源项目，它有一个公开的中央仓库，其他贡献者可以Fork（克隆）这个仓库作为你自己的私有仓库，开源项目维护者可以直接往中央仓库push代码，而代码贡献者只能将代码push到自己的私有仓库，只有项目维护者接受代码贡献者往中央仓库发起的pull request才会真正合入。

