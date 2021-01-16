---
title: git日常易被遗忘的命令
date: "2021-01-16"
spoiler: git
---

## git 设置

### 设置 git 命令输出为彩色

```git
git config --global color.ui auto
```

## 本地修改

### 显示与上次提交版本文件的不同

```git
git diff
```

### 把对某个文件的修改添加到下次提交中

```git
git add -p <file>
```

### 修改上次提交【针对未发布内容】

```git
git commit --amend
```

### 把当前分支未提交的修改移动到其他分支

```git
git stash
git checkout branch2
git stash pop
```

### 将 stashed changes 应用到当前分支

```git
git stash apply
```

## 合并与重置

### 放弃工作目录下的所有修改

```git
git reset --hard HEAD
```

### 移除缓存区的所有文件

```git
git reset HEAD
```

### 重置一个提交

```git
git revert <commit>
```

### 将 HEAD 重置到上一次提交的版本，并保留未提交的本地修改

```git
git reset --keep <commit>
```
