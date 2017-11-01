# gerrt rsync gitlab

1. install jdk 

2. java -jar gerrit-2.12.2.war init -d /opt/gerrit/review_site # 交互install replication plugin 

3. ssh -p 29418 liupeng@192.168.2.7 gerrit plugin ls # 确认已安装的插件

4. 打开http://192.168.2.7:7070 创建liupeng 项目并创建分支l_dev

5. 在gerrit 服务器添加到gitlab 项目的key 认证的配置如下，并把~/.ssh/id_rsa.pub 添加到gitlab

[gerrit@deploy etc]$ cat ~/.ssh/config 
        Host gitlab.x.com
        User liupeng 
        IdentityFile ~/.ssh/id_rsa 
        StrictHostKeyChecking no
        UserKnownHostsFile /dev/nulli


6. 下载gerrit-szjy  etc中配置文件 ，copy 到 /opt/gerrit/review_site/etc

7. ssh -p 29418 liupeng@192.168.2.7 gerrit plugin reload replication # 加载插件

8. ssh -p 29418 liupeng@192.168.2.7  replication list # 查看同步的项目





测试:

1. 把本机的key 添加到gerrit 用户中后 git clone ssh://liupeng@192.168.2.7:29418/liupeng -b l_dev
2. cd liupeng && echo test >README && git add . && git commit -m"add README file "
3. gitdir=$(git rev-parse --git-dir); scp -p -P 29418 liupeng@192.168.2.7:hooks/commit-msg ${gitdir}/hooks/
4. git commit --amend
5. git push origin HEAD:refs/for/l_dev
6. gerrit 系统审核+2 操作，正常在gitlab 中可以看到文件同步
