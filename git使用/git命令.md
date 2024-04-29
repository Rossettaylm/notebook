# git命令

#### .gitmodule文件下载

```
[submodule "third_party/googletest"]
	path = third_party/googletest
	url = https://github.com/google/googletest.git
[submodule "third_party/benchmark"]
	path = third_party/benchmark
	url = https://github.com/google/benchmark
```

```shell
# 递归下载，将third_party中的文件一起下载下来
git clone --recursive https://github.com/google/leveldb
```

#### 远程库

```shell
# 显示远程库
git remote -v 

# 添加远程库
git remote add <remote_name> <remote_addr>
git remote add origin https://github.com/Rossettaylm/leveldb

# 删除远程库
git remote remove <remote_name>

# 修改名称
git remote rename <old_name> <new_name>

# 修改地址
git remote set-url <remote_name> <new_url>

# 推送到远程库
git push <remote_name> <branch_name>
```

#### 分支操作

```shell
# 显示当前分支
git branch -l

# 新建分支
git branch <name>

# 切换到分支
git checkout <name>

# 删除分支
git branch -d <name>

# 新建并切换到该分支
git checkout -b <name>

```

#### 回退版本相关

* 撤销commit操作

```shell
    git reset [--mixed/soft/hard] HEAD^   撤销上次的提交

    # 如果想撤销N次commit,使用HEAD~N

    # 1、--mixed   不删除工作空间改动代码，撤销commit，并且撤销git add .操作。这个为默认参数,git reset --mixed HEAD^ 和git reset HEAD^ 效果相同
    # 2、--soft    不删除工作空间改动代码，撤销commit，不撤销git add .
    # 3、--hard    删除工作空间改动代码，撤销commit，撤销git add . 注意完成这个操作后，就恢复到了上一次的commit状态
    # Tips:如果只是想更改提交的注释信息,使用git commit --amend 即可进入vim编辑器修改,不用撤销commit操作
```

* 回退到指定的历史版本

```shell
    git log    # 获取历史提交的版本号 Tips:也可以在项目的.git/logs/refs/heads/master文件中查看操作日志

    git reset --hard e7a98b4eabc91afebd7e3f3d7c474276146c6c2f(版本号)    # 恢复到指定的历史版本

    //如果需要同步远程
    git push origin  <branch_name> -f    # 强制 push 更新远程分支
```

* 撤销回退,回到未来版本

```java
    git reflog  查看命令历史，以便确定要回到未来的哪个版本

    git reset --hard 版本号
```

 