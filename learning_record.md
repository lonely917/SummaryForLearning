#֪ʶ��ѧϰ
##������demo���
tabҳ֧�ֻ����л�
tabҳ��������ˢ��(listview ���� gridview�� ����ͼƬ��������)

##git ʹ��
���غ��ʰ汾����װ��
���ã���Ҫgit config ȥ�����û������롣
���ɱ���sshkey��
Ȼ����github�����key��
�����Ϳ��Ժ�Զ�˽����ˡ�
(��ʱ��clone ���� fetch����� �˿�22��443����Ӧ����Ҫ���ô���)

##git clone �����쳣����취
###״̬
Failed connect to github.com:443
ssh 22�˿� https�˿�443
��������ʧ�ܿ���������������������ã�ѧУ���߹�˾�����ã�����������22����443�˿�
���ǿ���ͨ�����ô���ķ�ʽ���ﵽ����Ŀ��
���ڹ�˾ʹ�á�$ git config --global http.proxy http://web-proxy.oa.com:8080�����ɡ�
�ο� http://stackoverflow.com/questions/18356502/github-failed-to-connect-to-github-443-windows-failed-to-connect-to-github

Updated the http.proxy key in git config by following command
git config --global http.proxy http[s]://userName:password@proxyaddress:port
Error - could not resolve proxy some@proxyaddress:port. It turned out my password had @ symbol in it.
Encode @ in your password to %40 because git splits the proxy setting by @
git config --global http.proxy http[s]://userName:password(encoded)@proxyaddress:port

##git �������ؿ�Զ�̿Ⲣ����
���ַ�����
@1ֱ��git bash ȥcloneԶ�̿��������Զ����ص����ز�������
@2�������ؿ⣬����git add ȥ����Զ�̿⡣
ʾ��
����git init
Զ�̽���Զ�̿�
Ȼ��remote add 
remote add [Դ����(�Զ���)] [Դ��ַ]
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git remote add origin https://github.com/lonely917/SummaryForLearning.git

�鿴Դ
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git remote
origin

�鿴Դ��ϸ��Ϣ
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git remote -v
origin  https://github.com/lonely917/SummaryForLearning.git (fetch)
origin  https://github.com/lonely917/SummaryForLearning.git (push)

����git show �� git status �Լ� git log(��û���ύ)
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

�½��ļ�readme.md
git add ��ӵ��汾��
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git add -A

git commit �ύ
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git commit -m "��� readme.md"
[master (root-commit) 50c177b] ��� readme.md
 1 file changed, 1 insertion(+)
 create mode 100644 readme.md

���͵�Զ�̿Ⲣ����(���Զ�̿⻹�ǿ�)
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
(-u �ǹ�������master��Զ��master���Ժ�Ϳ��Լ�)
����С��
С��

Ҫ����һ��Զ�̿⣬ʹ������git remote add origin git@server-name:path/repo-name.git��
������ʹ������git push -u origin master��һ������master��֧���������ݣ�
�˺�ÿ�α����ύ��ֻҪ�б�Ҫ���Ϳ���ʹ������git push origin master���������޸ģ�

��¡Զ�̿�
������һ��Ŀ¼�½���git bash������git clone��¡������
Administrator@WENBURGYAN-PC0 /D/pro/test
$ git clone https://github.com/lonely917/SummaryForLearning.git
Cloning into 'SummaryForLearning'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
Checking connectivity... done.

��¡��ɺ�����Ŀ¼�鿴
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

������ԭ��Ŀ¼��add һ���ļ� ���͵�Զ�̣�Ȼ���ٴ�Ŀ¼��fetch���൱�������ڶ�ذ칫��ͬ���ļ�
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git add .
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git commit -m "learning_summary �����ύ"
[master 1d04963] learning_summary �����ύ
 1 file changed, 130 insertions(+)
 create mode 100644 learning_record.md

����
Administrator@WENBURGYAN-PC0 /D/learning/learning_summary (master)
$ git push origin master
Username for 'https://github.com': lonely917
Password for 'https://lonely917@github.com':
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 2.14 KiB | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/lonely917/SummaryForLearning.git
   50c177b..1d04963  master -> master

����һ��Ŀ¼(������)����fetch�ͺϲ�
���Ƚ���fetch���������ص�����(û�кϲ�)
Administrator@WENBURGYAN-PC0 /D/pro/test/SummaryForLearning (master)
$ git fetch origin
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/lonely917/SummaryForLearning
   50c177b..1d04963  master     -> origin/master


����ͨ������鿴���غ�Զ�˵Ĳ��
Administrator@WENBURGYAN-PC0 /D/pro/test/SummaryForLearning (master)
$ git diff master origin/master
diff --git a/learning_record.md b/learning_record.md
new file mode 100644
index 0000000..3fae107
--- /dev/null
+++ b/learning_record.md

merge����
Administrator@WENBURGYAN-PC0 /D/pro/test/SummaryForLearning (master)
$ git merge origin/master
Updating 50c177b..1d04963
Fast-forward
 learning_record.md | 130 +++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 130 insertions(+)
 create mode 100644 learning_record.md

##git fetch merge ��git pull
http://www.oschina.net/translate/git-fetch-and-merge

##gitС��
�ֿ⽨�������ؿ⣬Զ�̿������push��fetch&merge��pull
��֧branch ��֧merge push pull �ع� pullrequest

##android permission & uses permission
http://blog.csdn.net/lilu_leo/article/details/6940941

##svn�������� 
ע��Ҫʹ��svn�Դ���diff���ߣ���������

##����adbֱ�Ӱ�װ����

##Activity���������ں�����ģʽ
newtask��ʽ��singleinstance�����Ļ���startactivityforresult��ֱ�ӷ��أ���onactivityresult�С�

#eclipse ini�����ļ�
��ʼ���Ѵ�С�����Ѵ�С����
��ʼ��̫������޷�����

##svn delete

##��������cmd����
1. ��win7�£�һ���Ҵ�������ʾ���ǲ���ϰ���ˡ�win+r��
2. ���������Ȱ�סshift����Ȼ���Ҽ��������һ��ѡ��ڴ˴�������ڡ�����������

##���õ�
��̬����
����
���
��Ϣͨ��
����
������
λͼ����
��ע��

##webview����swf
String url ="file:///android_asset/hoge.swf";

WebView wv=(WebView) findViewById(R.id.WebView01);

wv.getSettings().setPluginsEnabled(true);

wv.loadUrl(url);

##android anim����ʵ��