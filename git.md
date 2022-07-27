#### git

`git init` 初始化仓库

`git status -s` 精简展示状态

`git add .`  一键添加所有

`git commit -a -m""` 直接提交至仓库

`git reset HEAD filename` 从暂存区移除文件

`git rm -f filename`  从仓库、工作区同时移除文件

`git rm --cached filename` 从仓库移除，但工作区保留

`git log ` 查看提交历史

`git reset --hard <CommitID> `  根据提交的ID回退到指定版本

`git branch` 查看分支

`git branch ···` 创建分支

`git checkout ···` 切换到···分支

`git clone 远程仓库的地址` 克隆远程分支到本地

`git merge ···` 在master运行这一命令可合并这一分支到master上

 `git branch -d ···` 删除某一分支

`git remote add origin SSH地址` 关联仓库

`git push -u origin master` 将本地仓库中的内容推送到远程的origin仓库中

`git push -u origin 本地分支名：远程分支名`

![image-20220420194952395](C:\Users\hy020\AppData\Roaming\Typora\typora-user-images\image-20220420194952395.png)

