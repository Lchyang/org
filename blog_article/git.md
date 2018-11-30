
# Table of Contents

1.  [git 操作总结](#org8c3a7fe)
    1.  [初始化](#orgdc3be80)
        1.  [初始化版本库](#org24fb6f3)
        2.  [设置忽略文件](#orgc66cb7f)
        3.  [添加文件到版本库](#org490dab5)
        4.  [提交](#org94147f3)
    2.  [常用操作](#orgd7f32c2)
        1.  [撤销修改](#orgce5b8bc)
        2.  [分支](#org56d8a29)
        3.  [解决冲突](#org64edb75)


<a id="org8c3a7fe"></a>

# git 操作总结

前些时间用git操作经常出现一些莫名其妙的问题，对其中一些命令也不知道相互的关系，
为了弄清楚之间的联系，想深入了解一下并做此记录


<a id="orgdc3be80"></a>

## 初始化


<a id="org24fb6f3"></a>

### 初始化版本库

git init (会在根目录下生成.git 文件）


<a id="orgc66cb7f"></a>

### 设置忽略文件

1.  设置每个人都生效的忽略文件

    1.  在根目录下新建一个.gitignore 的文件
    2.  在命令行中执行 echo \*.ptxt>.gitignore (>左右不要有空格）也可以用vim手动添加
    3.  将.gitignore 文件加入版本库提交

2.  设置对自己生效的忽略文件

    修改 .git/info/exclude 文件 可使用正则表达式 例如：\*.[oa] 等价与\*.a 和\*.o


<a id="org490dab5"></a>

### 添加文件到版本库

1.  添加单个文件

    git add newfile.txt

2.  添加所有txt文件

    git add \*.txt

3.  添加所有文件

    git add . （包括子目录，但不包括空目录）
    git add -u (添加修改和删除的文件 不包括新文件）


<a id="org94147f3"></a>

### 提交

1.  提交所有

    git commit -m 'add all txt files' -a

2.  提交单个

    git commit -m 'add msg' readme.md

3.  增补提交

    git commit -C head -a &#x2013;amend 在本个节点上提交，不产出提交记录，不修改提交日志


<a id="orgd7f32c2"></a>

## 常用操作


<a id="orgce5b8bc"></a>

### 撤销修改

1.  撤销未提交的修改

    撤销1、2个文件 git checkout head readme.txt todo.txt
    撤销所有txt文件 git checkout head \*.txt
    撤销所有文件 git checkout head .

2.  撤销提交

    1.  反转提交
    
        反转提交但不提交 例如：反转最后一次提交 git revert &#x2013;no-commit head
    
    2.  复位
    
        取消暂存 git reset head 或 git reset head <file name>
        复位到head之前那个版本 git reset &#x2013;hard head^


<a id="org56d8a29"></a>

### 分支

1.  列出本地分支

    git branch

2.  列出所有分支

    git branch -a

3.  基于当前分支末梢创建新分支

    git branch <branch name>

4.  检出分支

    git checkout <branch name>

5.  基于当前分支末梢创建并检出分支

    git checkouit -b <branch name>

6.  基于某次提交、分支或标签创建分支

    git branch emputy bfe57de0 用来查看历史断面很方便
    git branch emputy2 emputy 

7.  合并分支

    1.  普通合并
    
        合并并提交 git merge <branch name>
        合并不提交 git merge &#x2013;no-commit  没有生效
    
    2.  压合合并
    
        压合合并后直接提交 git merge &#x2013;squash <branch name>
        压合合并后但不提交 git merge &#x2013;squash -no-commit <branch>
    
    3.  挑选合并
    
        挑选末次提交合并 但不提交 git cherry-pick &#x2013;no-commit 5b62b6  会有冲突

8.  重命名分支

    git branch -m <branch name> <new name> 不会覆盖已存在的同名分支
    git branch -M <branch name> <new name> 会覆盖已存在的同名分支

9.  删除分支

    git branch -d new2 如果分支没有合并会删除失败
    git branch -D new2 如果分支没有合并也会删除


<a id="org64edb75"></a>

### 解决冲突

1.  冲突很少时 直接编辑有冲突的文件提交

2.  冲突复杂时 git merge tool

    1.会生成.BACKUP, .BASE, .LOCAL 和.REMOTE四个文件
    2.然后自动调用冲突解决工具。
    3.解决之后手动删除 .orig 文件（冲突之前的文件备份）
    4.提交

