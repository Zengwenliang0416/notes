初始化仓库

git init

查看状态：

git status

创建分支：

git branch xxx(分支名)

切换分支：

git checkout xxx(分支名)

查看分支：

git branch [-v] #查看每个分支的最后一次提交 [--no-merged] #查看未合并的分支

合并方式

```makefile
# 默认 fast-forward ，HEAD 指针直接指向被合并的分支
git merge 

# 禁止快进式合并
git merge --no-ff 

git merge --squash 
```

