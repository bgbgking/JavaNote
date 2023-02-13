# Git

## git命令

### git初始化

git init

### 查看系统配置

git config --system --list

### 查看当前用户配置

git config --global --list

### 更改用户名和邮箱

git config --global user.name "用户名"

git config --global user.email "邮箱"

### 查看当前状态

git status

### 添加项目到缓存区

git add file.name

git add file1.name file2.name ...

git add .  （提交当前目录所有文件）

### 添加用户到本地仓库

.git文件 为本地仓库

git commit -m "描述"

### 查看版本

git log

git log --pretty=oneline  （版本号一行一行显示）

### 回退版本

git reset --hard 提交编号

### 查看历史操作

git reflog  (可以得到最新的版本编号)

### 克隆线上仓库到本地（http）

git clone 线上仓库地址

### 提交本地代码到线上仓库

git push

### 拉取线上版本

git pull

## 分支命令

### 查看分支

git branch

### 创建分支

git branch 分支名

### 切换分支

git checkout 分支名

当前分支前有*

### 删除分支

git branch -d 分支名

删除分支前要先退出要删除的分支

### 合并分支

git merge 被合并的分支名

## 提交项目

1.创建/进入个人分支

2.写代码

3.add到本地暂存区

4.commit到本地仓库

5.进入本地master分支

6.pull拉取远程仓库代码到本地master分支

7.merge合并本地个人分支（有冲突解决冲突）

8.push到远程仓库