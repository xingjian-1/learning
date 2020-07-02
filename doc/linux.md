## Linux命令：
#### 基础查看命令
###### cd --进目录, ls --当前目录下文件列表, ll --详细列表的目录, tail -f xx.log --打开动态日志文件，tail -100f xx.log --查看最近100行， less xx.log --打开文件 /108084 --搜索关键字 N --向上搜索，n --向下搜索，g --从第一条记录开始，G --到最近一条记录，q --退出，h --帮助，f --向下一页搜索，b --向上一页搜索
#### 用户创建命令
###### useradd nftuser -d /home/ap/nftuser --指定目录下创建用户，passwd nftuser 设置密码，su - niss --切换到niss用户， cat /etc/passwd nftuser --查看已创建的用户，chown -R nftuser ap/ --给用户nftuser赋予有操作ap目录下文件权限，cd ~ --进入当前用户的根目录，ls -lrt --表示按修改时间倒序输出，最新修改的文件会排在下面(没有加 -r 的时候排在上面)，telnet 10.30.9.98 13088 --telnet ip port测试是否连通，find -name bdeboot.sh --搜索文件，
#### 文件相关命令
###### mkdir test --在当前目录下创建test文件夹，cp test test.bak --备份test文件，rm -rf test --删除文件，rm -r test --删除指定目录，ls -la --查看隐藏文件列表,vi test.sh --编辑文件，WQ！--保存退出，:q! --不保存退出,source .test.sh --修改环境变量配置文件时保存之后 使其生效。scp khftp@10.0.0.209:/khftp/文件目录+要下载的文件名 /home/ap/nft(文件下载到目标目录)，tar -xvf 文件名 --解压压缩包，rpm -ivh ksh_*.rpm --安装文件，whereis ksh_*.sh --查看shell脚本文件地址，file test.sh --查看文件编码，echo $JAVA_HOME --查看jdkJAVA_HOME的地址，./fullinstall.sh nodeid=108084,dbip=10.30.9.98 --Linux系统里面执行带参数的安装脚本文件安装软件。
