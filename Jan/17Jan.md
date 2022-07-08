# 1.17工作日报

| 内容         | 时间        |
| ------------ | ----------- |
| 入职手续     | 10:00-11:00 |
| 实习目标设定 | 11:00-12:00 |
| 配置企业微信 | 14:00-15:00 |
| 熟悉Ubuntu   | 15:00-15:30 |
| 学习Git      | 15:30-19:00 |

***

[Git参考资料](https://backlog.com/git-tutorial/tw/intro/intro1_1.html)

### 一、名词解释

* Git:版本控制工具，为linux内核开发（分散式）

* Repository:数据库（本地repo push to 远端repo，远端repo pull to 本地repo）

* Commit:提交更新
  标准格式：
      • 提交时修改内容的摘要
      • 空行
      • 修改的理由

* Working Tree:工作目录

* Index:索引，从工作目录向数据库提交的暂存区域，可以避免不必要的提交

* Push:推送，上传本地端的修改历史到远端数据库

* Clone:复制，从远端数据库复制历史纪录和提交到本地数据库

* Pull:拉取，从远端数据库更新到本地数据库

  

### 二、Getting Start
| 步骤           | 命令                                                         |
| -------------- | ------------------------------------------------------------ |
| 安装git        | sudo apt install sit                                         |
| 初始设定       | git config --global user.name "maoyuxuan"                    |
|                | git config --global user.email "yuxuan_mao@mail.sjtu.edu.cn" |
| 新建数据库     | mkdir Gittutorial                                            |
| (git init)     | cd Gittutorial                                               |
|                | git init                                                     |
| 安装gitk以调试 | sudo apt-get install gitk                                    |
| 提交目录       | 在文件夹下新建文本文件sample.txt，并键入”git commit learning sample”. |
|                | *参考https://blog.csdn.net/w	eixin_44724865/article/details/98174155* |
|                | 确认工作目录状态：	 git status                            |
|                | 将文件加入索引：		git add sample.txt                   |
|                | (添加所有文件：		   git add .)                         |
|                | 提交					            git commit -m “first commit” |
|                | 确认历史提交记录：	git log                                |



### 三、共享数据库Repository
[Github参考资料](https://progressbar.tw/posts/3)

| 操作                   | 步骤                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 在github上建立Repo     | Owner : 這裡已設定好是使用者名稱                             |
|                        | Repository name : 仓库名稱                                   |
|                        | Description : 仓库的相關描述                                 |
|                        | Public/Private:公开或隐私                                    |
|                        | Add.gitgnore: GitHub的档案管理功能，可以隐藏某些信息         |
|                        | Add a lincense:使用授权                                      |
| 绑定git与github:       | 本地电脑生成密钥:ssh-keygen -t rsa -C "yuxuan_mao@mail.sjtu.edu.cn" |
|                        | *github不再支持输入用户名和密码，只能加密钥*                 |
|                        | *安装剪贴板工具xclip		sudo apt install xclip*          |
|                        | 定位到密钥目录			cd .ssh                            |
|                        | 剪切密钥到剪贴板			xclip -selection c id_rsa.pub    |
|                        | 在github-setting-SSH and GPG keys中新建SSH keys，将剪贴板内容粘贴进去 |
|                        | git验证（仓库目录）		ssh -T git@github.com             |
| 从本地push到远程Repo   | 为数据库SSH链接取别名	git remote add github git@github.com:zzMiao123/Gittutorial.git |
|                        | git push github  master                                      |
|                        | *用别名*                                                     |
| 从远程Repo clone到本地 | git clone (-b <分支名>)                                      |
|                        |                                                              |

git默认分支为master，github默认分支为main，应该一直用同一个
本地分支名映射到远程分支名：HEAD:main 

### 四、合并修改记录
**如果别人更改了数据库，自己的数据库不是在最新的基础上改，上传就会被拒绝。**



### 五、分支

* Inegration分支：稳定主线
* Topic分支：开发功能建立的分支
* 合并	:merge、rebase
* 分支分类：
  * main分支：master（管理发布）、develop（日常开发）
  * feature分支：开发功能，基本不需要共享
  * release分支：为最后的发布准备，修复错误
  * hot fix分支：紧急修改

| 操作               | 命令                            |
| ------------------ | ------------------------------- |
| 切换分支           | git checkout `branchname`       |
| 新建分支           | git branch `newbranchname`      |
| *或：*             | git checkout -b `newbranchname` |
| 将分支2合并到分支1 | (在分支1下) git merge `branch2` |
| 删除分支           | git branch -d `branchename`     |
|                    |                                 |

