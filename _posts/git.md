[TOC]





### pdf阅读

Zetero+https://github.com/windingwind/zotero-pdf-translate

max_depth = 3
learning_rate = 0.05
min_child_weight = 250
subsample = 0.7
colsample_bytree = 0.7
n_estimators = 200

### 文件传输

```sh
hadoop fs -ls viewfs://hadoop-meituan/user/h
adoop-grocerygoodsalgo/hanweidong02
```

### git 基本操作

```sh
# 撤销提交
git reset --soft HEAD^
# 撤销add
git reset HEAD .
# 版本回退
git reset --hard commit_id
# 删除远程分支
git push origin --delete main
# 查找记录，用于回退后撤销
git reflog
```

 ### git多个令牌配置

```shell
# 首先移除全局配置
git config --global --unset user.name
git config --global --unset user.email
# 生成新的密钥对（注意生成之前更改密钥文件名）
ssh-keygen -t rsa -C
ssh-agent bash
ssh-add ~/.ssh/id_rsa	

chmod go-w /home/hadoop-grocerygoodsalgo/.ssh/config
```

在.ssh目录下新建一个config文件：

```shell
#work github
Host sankuai	#托管网站域名别名
HostName git.sankuai.com	#托管网站域名
User zengjingran02	#托管网站的用户名
IdentityFile ~/.ssh/id_rsa	#使用的本地密钥文件
################################
Host github
HostName github.com
IdentityFile ~/.ssh/id_rsa_personal
User MercerJR

Host sankuai
HostName git.sankuai.com
IdentityFile ~/.ssh/id_rsa_zhz
User zhanghaozhou
```

使用：

```shell
git clone git@github.com:项目名.git	#也可以是git clone git@github:项目名.git
git config user.name ""
git config user.email ""
```

在 .git/config文件中修改：

```shell
[remote "origin"]
        url = git@github.com:项目名.git #也可以用域名别名
        fetch = +refs/heads/*:refs/remotes/origin/*
```

### git 不同分支做相同修改

```shell
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

### git 推送到远程分支

```
 git push <远程主机名> <本地分支名>:<远程分支名>
```

