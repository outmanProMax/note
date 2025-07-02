# 简介

- Git 是一个分布式版本控制工具，通常用来对软件开发过程中的源代码文件进行管理。通过Git仓库来存储和管理这些文件，Git仓库分为两种:
  1. 本地仓库：开发人员自己电脑上的Git仓库
  2. 远程仓库：远程服务器上的Git仓库
- `commit`：提交。将本地文件和版本信息保存到本地仓库
- `push`：推送，将本地仓库文件和版本信息上传到远程仓库
- `pull`：拉取，将远程仓库文件和版本信息瞎啊滋到本地仓库

# Git首次使用

1、访达右上方显示路径栏
2、进入用户文件路径并使用commmand+Shift+.显示隐藏文件
3、修改./gitconfig参数
4、修改./ssh/config参数

# Git常用命令

## Git全局设置

当安装 Git 后首先要做的事情是设置用户名称和 email 地址。这是非常重要的，因为每次 Git 提交都会使用该用户信息。

- `查看是否安装git`

  ```
  BASH
  git --version
  ```

- `设置用户命令信息（签名）`

  ```
  BASH
  git config --global user.name "outman2023"
  git config --global user.email "2876141934@qq.com"
  ```

- `查看配置信息命令`

  ```
  BASH
  git config --list
  ```

  通过上面的命令设置的信息都保存在`~/.gitconfig`文件中

## 获取Git仓库

要使用Git对我们的代码进行版本控制，首先需要获得Git仓库

- ```
  获取Git仓库命令
  ```

  - 本地初始化（不常用）

    ```
    BASH
    git init
    ```

  - 从远程仓库克隆（常用）

    ```
    BASH
    git clone [远程Git仓库地址]
    ```

    注意：仓库是不能嵌套的，不能在一个仓库目录内，克隆/创建另一个仓库。

## 工作区、暂存区、版本库概念

为了更好的学习Git，我们需要了解一下Git相关的一些概念，这些概念在后面的学习中会经常提到。

- 版本库：其实你在`git init`之后，会在当前文件夹创建一个**隐藏文件`.git`**，这个文件就是版本库，版本库中存储了很多的配置信息、日志信息和文件版本信息等
- 工作目录（工作区）：**包含`.git`文件夹的目录**就是工作目录，主要用于存放开发的代码
- 暂存区：一个临时保存修改文件的地方
  - `.git`文件夹内有很多文件，其中有一个名为**`index`的文件**就是暂存区，也可以叫做`stage`

## Git工作区中文件的状态

- Git工作目录下的文件存在两种状态
  - `untracked`未跟踪（未被纳入版本控制）
  - tracked已跟踪（被纳入版本控制）
    - `Unmodified`未修改状态
    - `Modified`已修改状态
    - `Staged`已暂存状态

这些文件的状态会随着我们执行Git的命令发生变化

## 本地仓库操作

### 本地仓库相关命令

|                命令                 |             功能             |
| :---------------------------------: | :--------------------------: |
| git config —global user.name 用户名 |         设置用户签名         |
| git config —global user.email 邮箱  |         设置用户签名         |
|              git init               |         初始化本地库         |
|             git status              |         查看文件状态         |
|         git add [文件名称]          |    将文件的修改加入暂存区    |
|        git reset [文件名称]         |    将暂存区的文件取消暂存    |
|      git reset —hard [版本号]       |        切换到指定版本        |
|      git commit -m “日志信息”       | 将暂存区的文件提交到版本库中 |
|               git log               |           查看日志           |
|             git reflog              |         查看历史记录         |

## 远程仓库操作

### 远程仓库常用命令

|                  命令                   |                        功能                        |
| :-------------------------------------: | :------------------------------------------------: |
|               git remote                |                    查看远程仓库                    |
|              git remote -v              |              查看当前所有远程地址别名              |
|    git remote add [short-name] [url]    |              添加远程仓库并为其取别名              |
|       git remote rm [short-name]        |                    移除远程仓库                    |
|             git clone [url]             |             从远程仓库克隆（首次拉取）             |
|  git pull [origin-name] [branch-name]   |                   从远程仓库拉取                   |
|  git push [origin-name] [branch-name]   |        推送到远程仓库（需使用ssh免密登录）         |
| git push -u [origin-name] [branch-name] | 推送到远程仓库(使用https登录，密码现在改为了token) |

Note：默认取[short-name]为origin

## 分支操作

### 简述

- 分支是Git使用过程中非常重要的概念
- 几乎所有的版本控制系统个都以某种形式支持分支
- 使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线
- Git的`master`分支并不是一个特殊的分支，它与其他分支没有区别
- 之所以几乎每一个仓库都有`master`分支，是因为git init命令默认创建它，而大多数人都懒得去改它
- 同一个仓库可以有多个分支，各个分支相互独立，互不干扰

### 分支操作常用命令

|                  命令                  |                    功能                    |
| :------------------------------------: | :----------------------------------------: |
|               git branch               |        查看分支：列出本地的所有分支        |
|             git branch -r              |        查看分支：列出所有的远程分支        |
|             git branch -a              |   查看分支：列出所有的本地分支和远程分支   |
|        git branch [branch-name]        |                  创建分支                  |
|       git checkout [branch-name]       |                  切换分支                  |
|  git push [short-Name] [branch-name]   |             推送至远程仓库分支             |
|        git merge [branch-name]         | 合并分支（将另一分支的内容融合到当前分支） |
|      git branch -d [branch-name]       |                  删除分支                  |
|      git branch -D [branch-name]       | 删除分支（即使该分支中进行了一些开发动作） |
| git push [short-Name] –d [branch-name] |            删除远程仓库中的分支            |

## 标签操作

### 简述

- Git中的标签，指的是某个分支特定时间点的状态。
- 通过标签，我们可以很方便地切换到标记时的状态（给我的感觉像是Linux的快照）

### 标签操作的常用命令

|              命令               |           功能           |
| :-----------------------------: | :----------------------: |
|             git tag             |         创建标签         |
|         git tag [name]          |         创建标签         |
|   git push [shortName] [name]   |   将标签推送到远程仓库   |
| git checkout -b [branch] [name] | 检出标签并加入一个分支中 |

# 总结

## 本地到远程的push（SSH的方法）

接着执行如下命令初始化一个本地仓库，如下图所示，多出了一个隐藏文件夹 .git，并默认进入主分支 main。

```
git init
```


然后将想要上传的文件放到这个本地仓库文件夹下，执行如下命令将文件添加到本地仓库：

```
git add .
```

接着将文件提交到本地仓库：

```
git commit -m "注释"
```


然后复制远程仓库的 SSH 地址，执行如下命令将本地仓库与远程仓库关联起来，关于 git remote add 命令可以参考这篇文章。

```
git remote add "别名" 远程仓库的SSH地址
```


将文件上传到 GitHub 的远程仓库：

```
git push "别名" master
```

# Note：解决ideal克隆时出现unable to access : Failed to connect to github.com port 443: Timed out

取消全局代理：
git config --global --unset http.proxy

git config --global --unset https.proxy
