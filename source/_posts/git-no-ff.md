---
title: git merge --no-ff
date: 2017-12-26 15:13:10
tags: Git
---

`--no-ff` 指的是强行关闭`fast-forward`方式。

`fast-forward`方式就是当条件允许的时候，git 直接把 HEAD 指针指向合并分支的头，完成合并。属于“快进方式”，不过这种情况如果删除分支，则会丢失分支信息。因为在这个过程中没有创建 commit。

`git merge --squash` 是用来把一些不必要 commit 进行压缩，比如说，你的 feature 在开发的时候写的 commit 很乱，那么我们合并的时候不希望把这些历史 commit 带过来，于是使用`--squash`进行合并，此时文件已经同合并后一样了，但不移动 HEAD，不提交。需要进行一次额外的 commit 来“总结”一下，然后完成最终的合并。

总结：
`--no-ff`：不使用 fast-forward 方式合并，保留分支的 commit 历史。
`--squash`：使用 squash 方式合并，把多次分支 commit 历史压缩为一次。



## 推荐用法

1.  开发分支合回 develop 需要 `--no-ff`。因为开发分支合到 develop 需要记录其分支信息，方便回退代码。
2.  develop 和 master 互相 merge ，不需要加参数，因为两个分支其实是同级的，不需要额外的分支合并信息(快进方式)。
3.  开发分支互相合并(虽然不建议这么做)，应该也不需要加参数。
4.  因为临时保存代码到 git 而 commit ，则在合并代码时使用 `--squash`，将不需要的 commit 合并到一起，并重新输入 commit-message。