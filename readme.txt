#NOTE：2020/04/11，Qihao LIU
#参考廖雪峰GIT教程
#常用命令：https://gitee.com/liaoxuefeng/learn-java/raw/master/teach/git-cheatsheet.pdf
#git官网：https://git-scm.com/
创建版本库
$ git init
$ git add readme.txt
$ git commit -m "wrote a readme file"
看文件内容
$ cat readme.txt
版本回退
$ git log
$ git reset --hard HEAD^
$ git reset --hard 1094a
$ git reflog
查看状态
$ git status
查看工作区和版本库里面最新版本的区别
$ git diff HEAD -- readme.txt 
撤销修改 丢弃工作区的修改
$ git checkout -- readme.txt
把暂存区的修改撤销掉（unstage），重新放回工作区：
$ git reset HEAD readme.txt
删除文件
$ rm test.txt
$ git commit -m "remove test.txt"
$ git checkout -- test.txt	（错删 还原工作区内容）

放在远程仓库
$ git remote add origin git@github.com:buaalqh/test.git
$ git push -u origin master
$ git push origin master（Git就会把master分支推送到远程库对应的远程分支上）
$ git remote/  git remote -v（查看远程库的信息）
$ git clone git@github.com:michaelliao/learngit.git（克隆远程仓库）
$ git checkout -b dev origin/dev（同事要在dev分支上开发，必须创建远程origin的dev分支到本地，用该命令创建本地dev分支）
	当此时我也需要提交到远程仓库时：
		$ git branch --set-upstream-to=origin/dev dev（指定本地dev分支与远程origin/dev分支的链接）；
		$ git pull（先用git pull把最新的提交从origin/dev抓下来）；
		合并，解决冲突，提交，push；
	$ git rebase（把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了）
		

分支管理：
	创建dev分支并切换分支（git checkout命令加上-b参数表示创建并切换）
		$ git checkout -b dev （$ git branch dev $ git checkout dev）；
		$ git switch -c dev；
	查看当前分支 $ git branch；
	切换回master分支 $ git checkout master / $ git switch master；
	把dev分支的工作成果合并到当前分支上 $ git merge dev ；
		（合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息）；
		（请注意--no-ff参数，表示禁用Fast forward：$ git merge --no-ff -m "merge with no-ff" dev，本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。）；
	当Git无法自动合并分支时，就必须首先解决冲突（解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。用git log --graph --pretty=oneline --abbrev-commit命令可以看到分支合并图）解决冲突后，再提交，合并完成；
	删除dev分支 $ git branch -d dev；

BUG分支：（思路1：stash自己的工作，在自己分支上改BUG，切换master复制修改，再恢复保存的stash）（分支：issue-101）
	思路2：
		工作只进行到一半，还没法提交，$ git stash；
		确定BUG分支：$ git checkout master（如：master）；
		创建临时分支：$ git checkout -b issue-101；修改，提交，切换回去，合并，删除临时分支，回到自己的分支：$ git switch dev；
		重新打开之前没完成的工作：（查看命令：$ git stash list）git stash apply + git stash drop/（git stash pop），恢复指定stash$ git stash apply stash@{0}；
		再修改自己分支上同样的BUG（复制一个特定的提交到当前分支）：$ git cherry-pick 4c805e2 
Feature分支：丢弃一个没有被合并过的分支，可以通过$ git branch -D feature-vulcan强行删除。

标签（tag）管理：（是指向某个commit的指针）版本号是v1.2，tag v1.2查找commit就行
	首先，切换到需要打标签的分支上$ git switch master；
	然后，敲命令git tag <name>就可以打一个新标签：$ git tag v1.0（默认标签是打在最新提交的commit上的）
	还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：$ git tag -a v0.1 -m "version 0.1 released" 1094adb
	想给历史上的某个commit打标签：
		方法是找到历史提交的commit id：$ git log --pretty=oneline --abbrev-commit；
		然后打上就可以了：$ git tag v0.9 f52c633；
	查看所有标签：$ git tag；
	注意，标签不是按时间顺序列出，而是按字母排序的。可以用git show <tagname>查看标签信息：$ git show v0.9；
	删除标签：$ git tag -d v0.1（创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。）
	要推送某个标签到远程：$ git push origin v1.0 （推送所有tags：$ git push origin --tags）
		如果标签已经推送到远程：$ git tag -d v0.9（删除本地tag），$ git push origin :refs/tags/v0.9（删除远程tag）

使用github：Fork别人的项目，从自己账户下clone，希望官方接收自己的修改就点pull request；

忽略特殊文件：https://github.com/github/gitignore，创建特殊的.gitignore文件（把要忽略的文件名称填进去）
	强制添加被忽略文件：$ git add -f App.class 或者查看.gitignore文件：$ git check-ignore -v App.class

配置别名：偷懒输入命令行（如果敲git st就表示git status那就简单多了）
	配置文件：.git/config。别名就在[alias]后面，要删除别名，直接把对应的行删掉即可。配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。
	$ git config --global alias.st status
	$ git config --global alias.co checkout
	$ git config --global alias.ci commit
	$ git config --global alias.br branch
	$ git config --global alias.unstage 'reset HEAD
	$ git config --global alias.last 'log -1'（配置一个git last，让其显示最后一次提交信息）
	git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

搭建git服务器：（一台运行Linux的机器，强烈推荐用Ubuntu或Debian）
	$ sudo apt-get install git
	$ sudo adduser git （创建一个git用户，用来运行git服务）
	第三步，创建证书登录：收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个。（如果团队有几百号人，就没法这么玩了，这时，可以用Gitosis来管理公钥。）
	第四步，初始化Git仓库。先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：$ sudo git init --bare sample.git （Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。）
	然后，把owner改为git：$ sudo chown -R git:git sample.git
	第五步，禁用shell登录。（出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：git:x:1001:1001:,,,:/home/git:/bin/bash）改为：git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
	第六步，克隆远程仓库：$ git clone git@server:/srv/sample.git

使用SourceTree：git的图形界面 
	
	

