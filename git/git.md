```makefile
git init

查看状态：

git status

创建分支：

git branch xxx(分支名)

切换分支：

git checkout xxx(分支名)

查看分支：

git branch [-v] #查看每个分支的最后一次提交 [--no-merged] #查看未合并的分支

删除本地分支
git branch -d localBranchName

删除远程分支
git push origin --delete remoteBranchName

```

## 合并方式

```makefile
# 默认 fast-forward ，HEAD 指针直接指向被合并的分支
git merge [branchName]

# 禁止快进式合并
git merge --no-ff [branchName]

git merge --squash [branchName]
```



## 删除git上传时的一些文件或者文件夹

> 在git上传的过程中，可能会发生一些不需要的文件传上去了，手动去删除很麻烦，本文提供三种方法删除。

### 方法1：可视化工具提交代码文件

最直接的办法使用可视化提交工具，适合于任何人，提交时候不要勾选不想上传的文件

### 方法 2：项目目录设置.gitignore

不论是git/svn，对于无关的文件包括项目编译过程中的中间文件，我们都选择忽略处理，这样就不会提交到仓库了。
仅针对git的处理就是设置.gitignore文件。

#### .gitignore配置规则(正则表达式)

用的时候去查。

### 方法3：全局设置忽略

```makefile
git config --list
vi ~/.gitconfig
touch ~/.gitignore_global
vi ~/.gitignore_global
open ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
git config --list
core.excludesfile = /Users/[username]/.gitignore_global
find . -name .DS_Store -print0 | xargs -0 git rm -f --ignore-unmatch
```

