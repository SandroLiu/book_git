# Git的使用

- 代码拉取  
  - 服务器 -> 本地版本库(代码仓库) -> 本地代码
- 代码提交
  - 本地代码 -> 本地版本库(代码仓库) -> 服务器

## 工作流程
- 开发人员1
    - Clone 从服务器获取初始代码
    - 编写代码
    - commit 提交到本地版本库
    - PULL 从服务器获取最新代码
    - PUSH 将本地版本库提交到服务器
- 开发人员2
    - Clone 从服务器获取初始代码
    - 编写代码
    - commit 提交到本地版本库
    - PULL 从服务器获取最新代码
    - PUSH 将本地版本库提交到服务器

## 命令行操作
1. 初始化一个代码仓库
    - git init
2. 如果想使用git，必须给git配置用户名和邮箱
    - 给当前的git仓库配置用户名和邮箱
        - git config user.name "sandro"
        - git config user.email "shuai.liu@nanochap.com"
    - 配置全局的用户名和邮箱
        - git config --global user.name "sandro"
        - git config --global user.email "shuai.liu@nanochap.com"  

　　当然有些时候我们的某一个项目想要用特定的邮箱，这个时候只需切换到你的项目，以上代码把 –global 参数去除，再重新执行一遍就ok了
3. 初始化项目
    - touch main.m: 创建了main.m
4. 将修改的代码添加到暂缓区
    - git add main.m: 将main.m添加到暂缓区
    - git add .: 将不在暂缓区的所有内容添加到暂缓区
    - git rm -cached: 移除暂缓区
5. 将暂缓区的代码提交到本地版本库
    - git commit -m "注释" 文件名
    - git commit -m "初始化项目" main.m
6. 删除文件
    - git rm main.m: 将main.m删除
7. 将代码提交到服务器
    - git push origin master 把本地代码推到远程master分支
    - git pull origin master 把远程最新的代码更新到本地
8. 查看版本信息
    - git log: 版本号是由sha1算法生成的40位哈希值
    - git reflog: 可以查看所有版本回退的操作
9. 版本回退
    - 没有提交到暂缓区时
        - git reset --hard HEAD: 回到当前的版本 --hard 强制回退，HEAD默认回退当前分支
        - git reset --hard HEAD^: 回到上一个版本
        - git reset --hard HEAD^^: 回到上上个版本
        - git reset --hard HEAD~100: 回到前100个版本
        - git reset --hard 版本号(前5位，重复了接着写)
10. 查看文件状态
    - git status
        - 红色文件名: 该文件被添加或者被修改，但是没有被添加到git的暂缓区
        - 绿色: 该文件在暂缓区，但是没有提交到本地版本库

## 分支

- git branch: 查看当前分支情况
- git branch a: 新建一个名字叫a的分支，这时候分支a跟分支master是一模一样的
    - 查看分支情况
        - a
        - \* master
    - \* 代表在当前分支
- git checkout a: 切换到分支a
- git checkout -b a: 新建一个a分支，并且自动切换到a分支
- 合并分支(将a分支的内容合并到master分支上)
    - 先切换到master分支上
    - git merge a 将a分支合并到master上
- git branch -d a: 将a分支删除，但是如果a分支的代码还没有合并到master上时是无法删除的
- git branch -D a: 强制删除a分支
- git branch -r: 查看远程分支列表
- git push origin :a : 删除远程分支a
- git checkout develop origin/develop : 远程分支有个 develop ，而本地没有，你想把远程的 develop 分支迁到本地
- git checkout -b develop origin/develop : 把远程分支迁到本地顺便切换到该分支

## 添加标签

　　我们在客户端开发的时候经常有版本的概念，比如v1.0、v1.1之类的，不同的版本肯定对应不同的代码，所以我一般要给我们的代码加上标签，这样假设v1.1版本出了一个新bug，但是又不晓得v1.0是不是有这个bug，有了标签就可以顺利切换到v1.0的代码，重新打个包测试了  
- git tag v1.0: 在当前代码状态下新建了一个v1.0标签
- git tag: 查看历史tag记录
- git checkout v1.0: 切换到1.0代码的状态


## 基本的团队协作流程

　　一般来说，如果你是一个人开发，可能只需要 master、develop 两个分支就 ok 了，平时开发在 develop 分支进行，开发完成之后，发布之前合并到 master 分支，这个流程没啥大问题。  

　　如果你是 3、5 个人，那就不一样了，有人说也没多大问题啊，直接可以新建 A、B、C 三个人的分支啊，每人各自开发各自的分支，然后开发完成之后再逐步合并到 master 分支。然而现实却是，你正在某个分支开发某个功能呢，这时候突然发现线上有一个很严重的 bug ，不得不停下手头的工作优先处理 bug ，而且很多时候多人协作下如果没有一个规范，很容易产生问题，所以多人协作下的分支管理规范很重要，就跟代码规范一样重要，以下就跟大家推荐一种我们内部在使用的一种分支管理流程 Git Flow。  
![](images/gitflow.png)

一般开发来说，大部分情况下都会拥有两个分支 master 和 develop，他们的职责分别是：

master：永远处在即将发布(production-ready)状态

develop：最新的开发状态

确切的说 master、develop 分支大部分情况下都会保持一致，只有在上线前的测试阶段 develop 比 master 的代码要多，一旦测试没问题，准备发布了，这时候会将 develop 合并到 master 上。

但是我们发布之后又会进行下一版本的功能开发，开发中间可能又会遇到需要紧急修复 bug ，一个功能开发完成之后突然需求变动了等情况，所以 Git Flow 除了以上 master 和 develop 两个主要分支以外，还提出了以下三个辅助分支：

feature: 开发新功能的分支, 基于 develop, 完成后 merge 回 develop

release: 准备要发布版本的分支, 用来修复 bug，基于 develop，完成后 merge 回 develop 和 master

hotfix: 修复 master 上的问题, 等不及 release 版本就必须马上上线. 基于 master, 完成后 merge 回 master 和 develop

什么意思呢？

举个例子，假设我们已经有 master 和 develop 两个分支了，这个时候我们准备做一个功能 A，第一步我们要做的，就是基于 develop 分支新建个分支：

git branch feature/A

看到了吧，其实就是一个规范，规定了所有开发的功能分支都以 feature 为前缀。

但是这个时候做着做着发现线上有一个紧急的 bug 需要修复，那赶紧停下手头的工作，立刻切换到 master 分支，然后再此基础上新建一个分支：

git branch hotfix/B

代表新建了一个紧急修复分支，修复完成之后直接合并到 develop 和 master ，然后发布。

然后再切回我们的 feature/A 分支继续着我们的开发，如果开发完了，那么合并回 develop 分支，然后在 develop 分支属于测试环境，跟后端对接并且测试的差不多了，感觉可以发布到正式环境了，这个时候再新建一个 release 分支：

git branch release/1.0

这个时候所有的 api、数据等都是正式环境，然后在这个分支上进行最后的测试，发现 bug 直接进行修改，直到测试 ok 达到了发布的标准，最后把该分支合并到 develop 和 master 然后进行发布。

## git 工作原理

- 工作区: 仓库文件夹里除了.git目录以外的内容 .git的同级目录及同级目录的子目录
- 版本库: .git目录，用于存储记录版本信息
    - 暂缓区(stage)
    - 分支(默认master): git自动创建的第一个分支
    - HEAD指针: 用于指向当前分支

### git add 和 git commit 的原理
- git add: 把修改或者新添加的文件添加到暂缓区
- git commit: 把暂缓区的所有内容提交到当前分支

## 给命令行起别名

- git config alias.st "status" 给status起别名
- git config --global alias.st "status" 给全局起别名

## 提交代码到GitHub上

- 获取SSH秘钥
    - 终端输入 ssh-keygen -t rsa 生成公私秘钥
    - 生成的文件在 ~/.ssh ~是/Users/liushuai
- 查看公钥
    - cd ~/.ssh
    - cat id_rsa.pub
- 查看ssh是否添加成功
    - ssh -T git@github.com

## 共享版本库

- git 服务器可以作为共享版本库，git服务器的搭建非常繁琐
- 可以把代码托管到GitHub上和OSChina上
- 一个文件夹
- 一个U盘

#### 一个文件夹作为共享版本库

1. git init --bare 将一个文件夹作为服务器
2. 将共享版本库的所有内容下载到本地
    - git clone 共享版本库地址
3. 忽略文件
    - touch .gitignore 创建忽略文件

#### 在GitHub创建项目
- 将公钥添加到GitHub上

- 在GitHub上新建项目然后clone到本地
    - 从GitHub上 新建一个项目
        - 新建项目
        - clone到本地
            - git clone git@github.com:SandroLiu/test.git
        - 配置git用户名和邮箱
        - 添加忽略文件
        - 添加xcode项目，因为已经是git目录下，所以创建xcode时添加版本库的选项是点不了
- 将本地已有的项目提交到GitHub上
    - 在GitHub上新建一个项目 命令行演练
    - git remote add origin git@github.com:stormzhang/test.git
    - git pull origin master 第一次拉取可能会出现错误则执行下面代码
        git pull origin master --allow-unrelated-histories
    - git push origin master 提交代码到服务器
- git remote -v 查看本项目的远程仓库

