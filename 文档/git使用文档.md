#git使用文档

- 认真学习到git教程的创建与合并分支，后期的还未做。

[toc]

##gitlab查看权限

- 在Your projects后面跟随了项目的所有的权限等级.

###gitlab权限登记

Gitlab权限管理
Gitlab用户在组中有五种权限：Guest、Reporter、Developer、Master、Owner

Guest：可以创建issue、发表评论，不能读写版本库
Reporter：可以克隆代码，不能提交，QA、PM可以赋予这个权限
Developer：可以克隆代码、开发、提交、push(但是提交master会遇到分支保护,只能开发分支,不能提交master,还是需要Maintainer权限)，RD可以赋予这个权限
Master(还是需要Maintainer)：可以创建项目、添加tag、保护分支、添加项目成员、编辑项目，核心RD负责人可以赋予这个权限
Owner：可以设置项目访问权限 - Visibility Level、删除项目、迁移项目、管理组成员，开发组leader可以赋予这个权限
Gitlab中的组和项目有三种访问权限：Private、Internal、Public

Private：只有组成员才能看到
Internal：只要登录的用户就能看到
Public：所有人都能看到
开源项目和组设置的是Internal

##git初级使用

- 对于已经安装的git，知道到建立的目录创建本地git仓库：$ **git init**
- 将文件添加到git仓$:**git add 文件名（.代表添加了所有改动的文件）**
> 把文件修改添加到暂存区(Stage)。
- remark $: **git commit -m "本次提交说明"**
> 把暂存区的所有的内容提交到当前分支。
- 查看仓库状态：**git status**
- 查看上次修改了什么： **git diff**
- 查看提交记录：**git log**
- 查看具体会退的版本号：**git log --pretty=oneline**
- 回退版本，	HEAD表示当前版本，^符号表示前一个，写数字100表示前一百个：**git reset --hard HEAD^**
> 注意：git仓库回退了，但是本地的代码可能并没有回退

- 根据git log的版本号来回退：** git reset --hard 版本号**
- 当产生回退，找不到以前的版本好的时候，查看所有的执行log，可以执行：**git reflog**
- 将工作区的所有代码撤销：**git chekout -- 文件名**
> 注意--是代表当前版本，如果没有就是说的主分支，注意命令错误
> 如果已经提交到工暂存区， 那么可以使用**git reset HEAD file**回到场景工作区，然后在使用git checkout

- 删除git暂存区的文件：git rm file
- 从暂存区恢复工作区误删的文件： git checkout -- file
- 把本地的git仓库关联到远程的git仓库：***$ git remote add origin git@github.com:michaelliao/learngit.git*
- 第一次把本地git仓库推送到远程仓库：**$ git push -u origin master**
- 推送最新修改：**git push origin master**
- 注意使用http每次都需要输入账号密码，除非使用第三方工具，如IDEA自动帮忙验证，所以一般使用ssh，更快也无需验证，不过有的平台只开放http接口，就无法使用ssh。
- 修改git的config，即可切换http和ssh，
- 删除ssh，可以直接点击前往：～/.ssh进行删除

###分支管理

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>或者git switch <name>

- 切换分支的时候,要先将工作add和commit到工作区 否则切换将不会变动

创建+切换分支：git checkout -b <name>或者git switch -c <name>

合并某分支到当前分支：git merge <name>

- 需要先切换到master,再去合并分支,检查合并完成在删除分支

删除分支：git branch -d <name>

- 如果各自在不同的分支提交了，在同一个文件修改了就会造成冲突，git用**<<<<<<<，=======，>>>>>>>**标记了不同的冲突，我们要合并冲突之后再次提交。
- **git log --graph**命令可以看到分支合并图
- **--no-ff**参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并
- 当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场；

在master分支上修复的bug，想要合并到当前dev分支，可以用git cherry-pick <commit>命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

- 开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。

- **git rebase试** rebase操作可以把本地未push的分叉提交历史整理成直线；

rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

忽略某些文件时，需要编写.gitignore；

.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！


- [git常用命令](https://gitee.com/liaoxuefeng/learn-java/raw/master/teach/git-cheatsheet.pdf)
####tag标签

命令git tag <tagname>用于新建一个标签，默认为HEAD，也可以指定一个commit id；

命令git tag -a <tagname> -m "blablabla..."可以指定标签信息；

命令git tag可以查看所有标签。

命令git push origin <tagname>可以推送一个本地标签；

命令git push origin --tags可以推送全部未推送过的本地标签；

命令git tag -d <tagname>可以删除一个本地标签；

命令git push origin :refs/tags/<tagname>可以删除一个远程标签。

####多人协作

查看远程库信息，使用git remote -v；

本地新建的分支如果不推送到远程，对其他人就是不可见的；

从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；

在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；

建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；

从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。


##忽略提交文件

- 项目根目录下:vim .gitignore

```OC
# Xcode
.DS_Store
*/build/*
xcuserdata
profile
DerivedData
.idea/
*.hmap
*.xccheckout
*.xcworkspace
!default.xcworkspace

#CocoaPods
Pods
!Podfile
!Podfile.lock
```

打开terminal (终端)
输入指令: cd ‘项目目录’
输入指令: vim .gitignore
把 开头的代码粘贴（command+v）
按 esc 键 ，输入指令 :wq (指令意思:保存并返回上一层)
此时，可以输入指令 sudo ls 查看目录下是否存在gitignore，有则表示成功
如果之前没有提交过第三方到远端，则按照正常情况，commit一下再push到远端就完成了
如果之前提交过得话，则输入指令 git rm -r Pods,然后再commit，再push

##ssh

###查看ssh

- $ cd ~/ssh
- $ cat id_rsa.pub


[廖雪峰的git教程](https://www.liaoxuefeng.com/wiki/896043488029600/896067008724000)

##mac配置多个git仓库

- 生成ssh-key： ssh-keygen -t rsa -C "Balopy@163.com"
> 可以填不同的邮箱
- 生成密钥后提示：
```OC
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/liugui/.ssh/id_rsa):
```
> 如果已经生成了id_rsa,那么可以新生成id_rsa_github这种用于区分，生成两套密钥

[Mac下配置多个Git账户](https://juejin.im/post/6844903880837300238)

1.回退版本问题
* 查看提交的版本, $: git log
* 根据提交版本commit的值head,回退版本$ git reset —hard hard的值
2.git迁移,需要重新配置公钥,然后下载.git文件,显示项目隐藏文件,直接用下载的.git文件替换项目的.git文件,直接在修改就可以了
3.生成公钥必须在固定文件夹下,.ssh里面,https是用不到公私钥的.
8.强制删除本地Git仓库:$ rm -rf .git
7.添加新的链接: git remote add origin http://39.106.170.183/iOS/HappyRentAHouse.git
6.删除当前git 链接:  git remote rm origin
5.本地强制覆盖网络的: git push -f origin master
4. git clone https://git.coding.net/vista_world/PaiPaiPet.git(地址)
3.(从远程仓库拉取数据)$:git remote add 项目名 地址     $:git remote -v  (查看版本)  $ git fetch 远程仓库地址 (从远程仓库拉去数据,会输入仓库的名字和密码)