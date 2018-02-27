---
title: LineageOS Gerrit 使用备忘
date: 2018-02-10 17:24:26
tags:
  - LineageOS
  - Gerrit
  - CodeReview
categories:
  - Note
---

## 前言
很久以前在 LineageOS 的 Gerrit 上提交过[一次代码](https://review.lineageos.org/#/c/173153/)，但是由于时间已经太久了，已经记不起来具体的操作了，今天正好又需要提交一个 patch，就重新过了一遍流程，也顺便记录一下备忘。

CodeReview 和 Gerrit 这两个名字应该是有不少上班族尤其是程序员知道，就是一个代码审核机制和页面端。


## 注册 LineageOS Gerrit 帐号
这个比较简单，一般来说用 Google 帐号登录就行了，需要注意的是个人开发者需要到个人设置里同意一个个人协议后才能往上面提交代码。

![](/images/gerrit1.jpg)

<!--more-->


## 配置 SSH Key
这部分其实是通用的，就跟在 GitHub 上添加 SSH Key 或者配置 VPS 免密码登录是一样的。如果你有现成的 SSH Key 也可以直接拿来用，建议单独创建一个。
使用命令 `ssh-keygen -t rsa -b 4096 -C "do4suki@gmail.com"` 生成一对密钥，中间会让你输入文件名，如果要跟其它的区分开，就设置成其它名字，不然用默认即可。

使用命令 `eval "$(ssh-agent -s)"; ssh-add ~/.ssh/id_rsa` 添加私钥到本地 SSH Agent，然后复制公钥 `~/.ssh/id_rsa.pub` 中的内容到页面上，保存即可。

![](/images/gerrit2.jpg)
![](/images/gerrit3.jpg)

添加完后测试一下是否生效，命令行输入 `ssh -p 29418 ikke@review.lineageos.org`，其中 ikke 换成注册 Gerrit 时候设置的用户名。没问题的话会返回以下内容，跟 GitHub 上是类似的。
```
  ****    Welcome to Gerrit Code Review    ****

  Hi YumeMichi, you have successfully connected over SSH.

  Unfortunately, interactive shells are disabled.
  To clone a hosted Git repository, use:

  git clone ssh://ikke@review.lineageos.org:29418/REPOSITORY_NAME.git

Connection to review.lineageos.org closed.
```

## 下载仓库代码
比如我今天是要往 `android_device_qcom_sepolicy` 这个项目里提交代码，那么就在项目列表里找到这个项目。

![](/images/gerrit4.jpg)

点击进入，因为 LineageOS 的 Gerrit 是不支持通过 SSH 的方式克隆项目的，另外 LineageOS 提供的 `commit-msg hook` 链接也是失效的，所以这里选择 `Clone` 和 `anonymous http`，复制生成好的链接 `git clone https://github.com/LineageOS/android_device_qcom_sepolicy`，在终端里执行就可以克隆下来了。

另外如果默认分支不是你要提交代码的分支的话，可以加上 `-b` 参数指定分支，比如我要提交到 `staging/lineage-15.1` 这个分支，就是 
`git clone https://github.com/LineageOS/android_device_qcom_sepolicy -b staging/lineage-15.1`。

进入项目目录里，手动下载 `commit-msg hook`，这里我使用 OmniROM 提供的。
```
cd android_device_qcom_sepolicy
curl -Lo .git/hooks/commit-msg http://gerrit.omnirom.org/tools/hooks/commit-msg
chmod u+x .git/hooks/commit-msg
```

作出修改，然后生成 commit，这时候 commit 里有带上 `Change-ID` 就可以了。

![](/images/gerrit5.jpg)
![](/images/gerrit6.jpg)


## 提交代码到 Gerrit
提交代码需要通过 SSH 链接，也就是前面项目页面里选择 `Clone` 和 `ssh` 后得到的命令里，取出链接即可，例如 `ssh://ikke@review.lineageos.org:29418/LineageOS/android_device_qcom_sepolicy`。

最后执行 push 操作即可，我已经提交过了这里就不再执行一遍了。
```
git push ssh://ikke@review.lineageos.org:29418/LineageOS/android_device_qcom_sepolicy HEAD:refs/for/staging/lineage-15.1
```

## 查看已提交的 commit
这时候就可以在首页刷出刚提交的 commit 了，或者在个人更改里也可以看到。

![](/images/gerrit7.jpg)
![](/images/gerrit8.jpg)


## 添加审阅人
提交后的代码，需要经过相关的审阅人员审阅后，如果没有问题，就会被合并到该分支的代码里。

![](/images/gerrit9.jpg)


## 更新提交信息
在提交未被合并之前，都可以随时进行修改，只要在本地修改后，通过 `git commit -a --amend` 合并到之前的提交里，然后重新 push 即可。注意一定要使用 amend 保证提交的 `Change-ID` 没有发生改变，如果 `Change-ID` 发生改变，就会被当作新的提交。

![](/images/gerrit10.jpg)


剩下的就是等待审阅人员审阅了。
