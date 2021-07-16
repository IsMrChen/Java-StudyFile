## 1. 合并分支代码

**合并步骤：**

1、进入要合并的分支（如开发分支合并到master，则进入master目录）

```
git checkout master
git pull
```

2、查看所有分支是否都pull下来了

```
git branch -a
```

3、使用merge合并开发分支 (这里分支名是指要准备合并到主分支的名称)

```
git merge 分支名
```

4、查看合并之后的状态

```
git status 
```

5、有冲突的话，通过IDE解决冲突；

6、解决冲突之后，将冲突文件提交暂存区

```
git add 冲突文件
```

7、提交merge之后的结果

```
git commit 
```

<p style='color:red'>如果不是使用git commit -m "备注" ，那么git会自动将合并的结果作为备注，提交本地仓库</p>

8、本地仓库代码提交远程仓库

```
git push
```

git将分支合并到分支，将master合并到分支的操作步骤是一样的。

将其他分支代码合并到当前分支也是一样的道理：

1.先切换到主分支

```
git checkout 分支名
```

2.合并到当前分支

```
git merge 要合并过来的分支
如果不行 使用命令：
git merge develop --allow-unrelated-histories
```



## 2. 设置用户名和密码

**查看用户名和邮箱地址：**

```shell
//查看用户名
git config user.name
//查看用户邮箱地址
git config user.email
```

**修改用户地址和邮箱：**

```shell
//修改用户名称
git config --global user.name "username"
//修改邮箱地址
git config --global user.email "email@xxx.com"
```

## 3. 拉取分支并提交到远程新分支

1、问题描述：用git管理代码的时，有些情况不希望改动主分支的代码，那么这时就需要创建一个分支来改动代码，也就是老大常说的你从master分支上拉一个，然后提交到你自己的新分支一般会这样命名：master_yourname或者master_需求

2、问题解决：

- 首先在gitlab或github上创建一个分支，假设名字叫test；
- 在本地创建一个分支，名字叫learn，指令是：`git branch learn` ;
- 当修改完代码后，提交到test分支的指令：`git push orign learn:test`

## 4. git如何删除远程分支

git push origin :远程分支名

origin后面有空格，相当于推送一个空的分支

## 5. git合并时遇到冲突或错误后取消合并

当合并分支时遇到错误或者冲突，分支旁边会多出“|MERGING”这个东西

![img](https://gitee.com/JongcyChen/PicBed/raw/master/img/a60d7777d92ea6726750c214327188ed.png)

有这个状态存在时，会导致后面想要再合并的时候提示如下

![img](https://gitee.com/JongcyChen/PicBed/raw/master/img/d7cdb9830d9d50a75b5257273e9eba7d.png)

所以需要先取消这次合并，使用“git merge --abort”命令

```
git merge --abort
```



![img](https://gitee.com/JongcyChen/PicBed/raw/master/img/7cfa9491053ce24ad574d0a1687f0845.png)

## 6.防止pull代码覆盖修改  采用 Stash暂存

今天pull的时候出现这个提示。

```
Your local changes would be overwritten by merge.
Commit, stash or revert them to proceed.
```

显示。如果Pull 成功会把我修改的代码覆盖

所以按照提示通过idea处理
第一步:储存我自己的修改内容:

![在这里插入图片描述](https://gitee.com/JongcyChen/PicBed/raw/master/img/2020100923003524.png)

先把 我们的修改 Stash 存储起来，操作完了会发现项目是修改前的
第二步：Pull
![在这里插入图片描述](https://gitee.com/JongcyChen/PicBed/raw/master/img/20201009230405500.png)
更新完成以后
第三步：取出我们的修改然后合并、提交。

![image-20210716094734923](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210716094734923.png)

![image-20210716094801427](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210716094801427.png)