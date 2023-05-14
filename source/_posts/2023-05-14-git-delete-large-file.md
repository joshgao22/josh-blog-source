---
title: Git 删除已提交的大文件
mathjax: false
comments: true
copyright: true
toc:
  enable: true
  number: true
  wrap: true
  expand_all: true
  max_depth: 4
tags:
  - Git
categories:
  - Git
abbrlink: a587d6e6
date: 2023-05-14 14:30:08
---

在使用 git 管理代码仓库的时候，有可能不小心提交了一些大文件，虽然后来从工作区删除了，但是已经于事无补，大文件的历史记录仍然保存在 git 仓库里。以下提供了基于文件大小排序的 Git 仓库文件搜索和删除方法。

# 查找大文件

在 Git 仓库目录下执行命令对归档的文件进行排序，并给出文件大小前 5 的文件 ID 等信息：

``` bash
git verify-pack -v .git/objects/pack/pack-*.idx | sort -k 3 -g | tail -5
```

输出结果如下，依次是文件 ID、文件类型、文件字节数、`size-in-packfil` 和 `offset-in-packfile`：

``` plain Example Output
10eaa37e6e1999cf5652e9b4c6b3ca5fa43a90b1 blob   14873202 11312961 77109113
c8aeb64369be1be657633e92d07dc49ed91fbdc1 blob   14873202 11313163 23914709
1ed41690c8dced0c2ac10b41fafbf23b72a081fd blob   17416456 749732 16400558
b7cb7ef09404b7514d04a5d0f56a7a99e96c072d blob   26776467 1877027 96312339
66e27e2c0159a8439a744d57f058d63dbe192e95 blob   552933714 543247488 117101156
```

<!-- more -->

根据文件的 ID（SHA1），查找文件路径：

``` bash
git rev-list --objects --all | grep <targrt_file_ID>
```

示例输入如下：

``` bash Example Input
git rev-list --objects --all | grep 66e27e2c0159a8439a744d57f058d63dbe192e95
```

输出结果如下，以此是文件 ID 和文件路径：

``` plain Example Output
66e27e2c0159a8439a744d57f058d63dbe192e95 projects/duifei/duifei_online_acq_par/duifei_online_acq_par_20230511.xpr.zip
```

# 将要删除的大文件从各个分支中移除

执行下面命令，将文件从分支的提交中移除

``` bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch <target_file_path>' --prune-empty --tag-name-filter cat -- --all
```

示例输入如下：

``` bash Example Input
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch projects/duifei/duifei_online_acq_par/duifei_online_acq_par_20230511.xpr.zip' --prune-empty --tag-name-filter cat -- --all
```

示例输出如下：

``` plain Example Output
WARNING: git-filter-branch has a glut of gotchas generating mangled history
         rewrites.  Hit Ctrl-C before proceeding to abort, then use an
         alternative filtering tool such as 'git filter-repo'
         (https://github.com/newren/git-filter-repo/) instead.  See the
         filter-branch manual page for more details; to squelch this warning,
         set FILTER_BRANCH_SQUELCH_WARNING=1.
Proceeding with filter-branch...

Rewrite 28271ddd7ce45bc13561bc7343664d937c267a2f (4/10) (1 seconds passed, remaining 1 predicted)    rm 'projects/duifei/duifei_online_acq_par/duifei_online_acq_par_20230511.xpr.zip'
Rewrite e6b6be1ada32ad2eff5f52b45372ed9274a04f30 (9/10) (1 seconds passed, remaining 0 predicted)
Ref 'refs/heads/master' was rewritten
WARNING: Ref 'refs/remotes/github/master' is unchanged
Ref 'refs/remotes/jgnas/master' was rewritten
Ref 'refs/remotes/localhost/master' was rewritten
Ref 'refs/remotes/origin/master' was rewritten
```

# 删除缓存下来的 ref 和 git 操作记录

``` bash
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
```

# 垃圾回收

上面 2 步把大文件的索引都切断了，这个时候进行垃圾回收，就可以很明显看到效果。

``` bash
git gc --prune=now
```

这时候的仓库中的大文件就已经被删除了，把仓库**推送到新的远程仓库**或者**强制推送到现在的远程仓库**都是可以的。

``` bash
git push --force
```

# 参考

[删除 git 仓库中无用大文件](https://www.cnblogs.com/oloroso/p/13367120.html)
