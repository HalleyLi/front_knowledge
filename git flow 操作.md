### git gitflow 相关操作

@(前端)

git status
git add 
git commit
以上三个指令可以重复
最终可
git push origin master

另一个人提交前需
git pull --rebase origin master

有冲突时 解决后
git add src/cm/cm.js
然后
git rebase --continue

开始一个新的feature
git checkout -b Halleys-feature master
然后我可以在这个分支上
git status
git add
git commit
在我离开时，最好将代码提交到中央库，这也是一个方便的备份
git push -u origin Halleys-feature
回来后完成了这个feature,在合并到mater之前，我需要先file a pull request 让其他组成员知道我做完了。但是首先我应该确保中央资源库有我最近的提交；
git push
然后我file the pull request 请求去合并Halleys-feature到master上，小组成员会自动被通知。 
如果其他人想在这个分支上修改东西时
git checkout master
git pull
git pull origin Halleys-feature
git push


GitFlow并没有添加超出功能分支工作流所需的任何新概念或命令，而是他将特定的角色分配给不同的分支，并定义他们应该如何和何时相互交互。  主要的不同就是项目的分支结构

**历史分支**
gitflow用两个分支来记录项目的历史。**master分支**来存储官方发布历史，**develop**作为提供新功能整合分支。我们可以非常方便给mater的所有提交记录用版本号打tag.

**feature branch**
每一个新的功能应该只包含在他的分支里，这个分支可以push到中央库用来备份或协作。但是和mater分支不一样的是，功能分支用develop分支作为他们的parent branch.当一个功能完成后，它应该merge into develop分支。功能分支永远不应该直接和master分支有关系。

**release branch**
当develop分支已经acquire足够的功能用于一个发布版本，你就应该从develop分支里fork一个发布分支。创建这个分支是为了下一个分支循环，所以在这个节点之后不应该添加任何新的功能（bug fixes，文档生成，其他发布相关任务除外），一旦完成准备发布。这个release分支应该合并到master分支并用版本号打tag.除此之外，这个分支也应该合并到develop分支上

**用这个重要的发布分支，可以协同一个团队打磨polish当前发布版本的同时，另一个团队同时进行下一个版本的发布做准备。**她也创建了明确定义的开发阶段。

**maintenance branches(hotfix)**
这个分支用来快速修复补丁生成版本。这是唯一一个直接从master分支作为parent创建而来的。一旦这个hotfix完成，他应该直接被合并到master和develop分支上，同时master分支应该更新版本号，打tag;

下面请看例子：
第一步，创建develop分支，最简单的方法就是，一个开发人员在本地创建一个空的develop分支并把它推到服务器上；
git branch develop
git push -u origin develop
这个develop分支会包含整个项目的历史，而master分支将只包含一个简略的版本。其他开发人员应该clone这个库后，并创建一个tranck branch for develop
git clone .../git
git checkout -b develop origin/develop

切换分支
git checkout develop

**开发新的功能**
每个人开发某些特定的新功能，则需要创建单独的分支为格子的功能。不是在master的基础上，他们应该是在develop的基础上创建他们的分支
git checkout -b some-feature develop

然后各自在这个分支上开发新功能并提交
git status 
git add <some-file>
git commit

当我完成了我的新功能后，当其他人正在使用pull requests请求，这时将是个最恰当的时间去open one asking to merge her feature into develop;否则的话，他可以将新功能合并到本地的develop分支上，并把它推到中央资源库中，如下
git pull origin develop
git checkout develop
git merge some-feature
git push
git branch -d some-feature
**解释：第一行命令可以确保develop分支在我们准备合并新的分支前是最新的up to date,一定要注意，新功能永远不应该直接被合并到master分支上**；

**下面我准备一个新的版本**
虽然其他人在他的功能分支上正在工作，但是我准备为这个项目发布一个官方的版本。就像功能开发一样，我需要用一个新的分支来封装发布准备（encapsulate the release preparations）;这一步也是发布版本号建立的地方；

git checkout -b release-0.1 develop

这个分支是一个清理发布，测试一切，更新文档，并为即将发布的版本做任何其他类型的准备的地方。
当我创建了这个分支，并将它推到中央库后，这个release版本就是 feature-frozen.任何尚在开发中的功能都将被推迟到下一个版本发布。

我在该分支下创建了一个发布文档等操作，然后我会将它合并到master和develop分支上，并删除这个release分支。注意，将该发布版本合并到develop分支上非常的重要，因为很多重要的更新或者文档更新等都添加在这个release分支上，他们对于新的功能也是可获取到的。同时，如果需要代码review,那么这个分支将是一个完美的地方for a pull request.

在该发布分支上做一系列操作后，通过git staus git add git commit等完成。然后
git checkout master
git merge release-1.0
git push
git checkout develop
git merge release-1.0
git push
git branch -d release-1.0

**release分支act as a buffer在功能开发和public releases.不管什么时候你合并一些东西到master分支的时候，你都应该tag the commit for easy reference**
git tag -a 1.0 -m "Initial public release" master
git push --tags

发布了这个发布版本后，我返回到develop分支为另一个同事开发的新功能发布下一个版本。也就是说，until an end-user opens a ticket complaining about a bug in the current release.to address the bug,我将会创建一个维护master的分支，fixes the issue with as many commits as necessary, 然后直接把它发布到master上。

git checkout -b issue-#001 master
//创建新的修改分支，并且解决相应的bug,通过一系列的git status add,commit 到该分支上

git checkout master
//然后切换到master分支上
git merge issue-#001
//将该分支内容合并到master分支上
git push
//推送到远程服务器上

像发布分支一样，维护分支包含重要的更新需要被包含到develop 分支上


**其他情况用到 `git commit --amend`**
​	当我需要追加到上次commit的东西时：
	1. `git log `查看一下最近的commit id
     2. `git reset --soft commit_id`到我需要修改的那一个commit记录
     3. 然后继续我要改的文件 `git add; git commit --amend; repo upload`




