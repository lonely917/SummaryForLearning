#知识项学习
##界面框架demo设计
tab页支持滑动切换
tab页内置下拉刷新(listview 或者 gridview， 传输图片或者文字)

##git 使用
下载合适版本，安装。
配置，需要git config 去设置用户名密码。
生成本地sshkey。
然后在github上添加key。
这样就可以和远端交互了。
(有时候clone 或者 fetch会出现 端口22或443不响应，需要设置代理)

##git clone 连接异常解决办法
###状态
Failed connect to github.com:443
ssh 22端口 https端口443
这里连接失败可能是由于所在网络的闲置，学校或者公司的设置，不允许连接22或者443端口
我们可以通过设置代理的方式来达到连接目的
我在公司使用“$ git config --global http.proxy http://web-proxy.oa.com:8080”即可。
参考 http://stackoverflow.com/questions/18356502/github-failed-to-connect-to-github-443-windows-failed-to-connect-to-github

Updated the http.proxy key in git config by following command
git config --global http.proxy http[s]://userName:password@proxyaddress:port
Error - could not resolve proxy some@proxyaddress:port. It turned out my password had @ symbol in it.
Encode @ in your password to %40 because git splits the proxy setting by @
git config --global http.proxy http[s]://userName:password(encoded)@proxyaddress:port

##git 建立本地库远程库并关联
两种方法：
@1直接git bash 去clone远程库下来，自动下载到本地并关联。
@2建立本地库，利用git add 去关联远程库。
示例
本地git init
远程建立远程库
然后remote add 
remote add [源名称(自定义)] [源地址]
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git remote add origin https://github.com/lonely917/SummaryForLearning.git

查看源
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git remote
origin

查看源详细信息
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git remote -v
origin  https://github.com/lonely917/SummaryForLearning.git (fetch)
origin  https://github.com/lonely917/SummaryForLearning.git (push)

测试git show 和 git status 以及 git log(还没有提交)
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git show
fatal: bad default revision 'HEAD'

Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git status
On branch master

Initial commit

nothing to commit (create/copy files and use "git add" to track)

Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git log
fatal: bad default revision 'HEAD'

新建文件readme.md
git add 添加到版本库
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git add -A

git commit 提交
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git commit -m "添加 readme.md"
[master (root-commit) 50c177b] 添加 readme.md
 1 file changed, 1 insertion(+)
 create mode 100644 readme.md

推送到远程库并关联(起初远程库还是空)
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git remote
origin

Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git push origin master
Username for 'https://github.com': lonely917
Password for 'https://lonely917@github.com':
Counting objects: 3, done.
Writing objects: 100% (3/3), 255 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/lonely917/SummaryForLearning.git
 * [new branch]      master -> master
(-u 是关联本地master和远程master，以后就可以简化)
推送小结
小结

要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；
关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

克隆远程库
在另外一个目录下进入git bash，利用git clone克隆岛本地
Administrator@WENBURGYAN-PC0 /D/pro/test
$ git clone https://github.com/lonely917/SummaryForLearning.git
Cloning into 'SummaryForLearning'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
Checking connectivity... done.

克隆完成后进入库目录查看
Administrator@WENBURGYAN-PC0 /D/pro/test
$ ls
Android-PullToRefresh  SummaryForLearning  ZrcListView

Administrator@WENBURGYAN-PC0 /D/pro/test
$ cd SummaryForLearning/

Administrator@WENBURGYAN-PC0 /D/pro/test/SummaryForLearning (master)
$ git remote
origin

Administrator@WENBURGYAN-PC0 /D/pro/test/SummaryForLearning (master)
$ git remote -v
origin  https://github.com/lonely917/SummaryForLearning.git (fetch)
origin  https://github.com/lonely917/SummaryForLearning.git (push)

我们在原先目录下add 一个文件 推送到远程，然后再此目录下fetch，相当于我们在多地办公，同步文件