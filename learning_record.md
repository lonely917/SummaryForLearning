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