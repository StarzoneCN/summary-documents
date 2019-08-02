# Git

## 1. Web hook

// todo

## 2. 概念篇

### 2.1 树对象

* 请看[Git 内部原理 - Git 对象](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%AF%B9%E8%B1%A1 '搜索"树对象"')



## 3. 命令篇

### 3.1 git log <font size="2">[✈][Git 基础 - 查看提交历史]</font>

#### 3.1.1 命令解释

* **git log**

  * `ZZ`：可退出命令界面（类似vi/vim）

* **git log -p -2**

  * `-p`：选项展开显示每次提交的内容差异，用 `-2` 则仅显示最近的两次更新

* **git log -p --word-diff**

  * `--word-diff`：获取单词层面上的对比 ，用于文本文件对比的时候很有效；代码中就没有什么用了；

* **git log -U1**

  * `-U1`：限制上下文显示行数为1行，默认是3行；

* **--pretty**

  * 设置不同的内置显示格式 - `oneline`、`short`、`full`、`fuller`

* **format**

  * 任意组合输出形式

    ```bash
    $ git log --pretty=format:"%h - %an, %ar : %s"
    ca82a6d - Scott Chacon, 11 months ago : changed the version number
    085bb3b - Scott Chacon, 11 months ago : removed unnecessary test code
    a11bef0 - Scott Chacon, 11 months ago : first commit
    ```

* **--since**

  * 时间过滤，eg：`git log --since=2.weeks` - 2周内；
  * *since*后也可以是具体的某一天（“`2008-01-15`”），或者是多久以前（“`2 years 1 day 3 minutes ago`”）

* `--author`

  * 筛选作者（修改人）

* `--committer`

  * 筛选提交人

* `--grep`

  * 过滤关键字，*注：如果要得到同时满足`--author`和`--grep`这两个选项搜索条件的提交，就必须用 `--all-match` 选项。否则，满足任意一个条件的提交都会被匹配出来*

#### 3.1.2 复杂的例子

```bash
# 2008 年 10 月期间，gitster提交的但未合并的测试脚本（位于项目的 t/ 目录下的文件）
$ git log --pretty="%h - %s" --author=gitster --since="2008-10-01" --before="2008-11-01" --no-merges -- t/
```

## 4. 权限控制
参考：[Git 权限控制][] / [Git 权限控制2][]


[Git 基础 - 查看提交历史]: https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%AF%B9%E8%B1%A1
[Git 权限控制]: https://blog.csdn.net/Smith_My_/article/details/74178111
[Git 权限控制2]:https://qidawu.github.io/2016/04/04/git-permissions/
