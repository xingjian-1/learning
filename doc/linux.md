## `Linux系统命令`
#### 查看日志常用命令
* cd -进当前用户主目录, cd home/ap/ -进入到指定目录 cd .. -返回上级目录
* ls -当前目录下文件列表, ll -详细列表的目录, 
* tail -f xx.log -打开动态日志文件，tail -100f xx.log -查看最近100行， 
* less xx.log -打开文件, /108084 -搜索关键字, N -向上搜索，n -向下搜索，g -从第一条记录开始，G -到最近一条记录，h -帮助，f -向下一页搜索，b -向上一页搜索，
* grep A0335G13S app_mSrv1.log -文件中搜索关键字
* q -退出，
#### 环境配置常用命令
* useradd nft -d /home/ap/nft -指定目录下创建用户，
* passwd nft -设置密码，
* su - niss -切换到niss用户，
* cat /etc/passwd nft -查看已创建的用户，
* cd ~ -进入当前用户的根目录。
* chmod 777 ./* -操作当前目录下所有文件的权限，chown -R nft ap/ -给用户nft赋予有操作ap目录下文件权限，chmod -R 777 /home/ap/niss/unload/scripts/test.sh  -授权(当前用户登录进去直接授权即可)。
* mkdir test -在当前目录下创建test文件夹，
* rm -rf test -删除文件夹，rm -r test -删除指定目录，
* cp test test.bak -备份test文件为test.bak。
* scp khftp@10.0.0.209:/khftp/文件目录+要下载的文件名 /home/ap/nft(文件下载到目标目录)，
* tar -xvf 文件名 -解压压缩包，unzip test.zip -解压zip包，
* rpm -ivh ksh_*.rpm -安装rpm文件，
* ./fullinstall.sh nodeid=108084,dbip=10.30.9.98 --Linux系统里面执行带参数的安装脚本文件安装软件
* env|grep TNS -搜索关键字,whereis test.sh -查看shell脚本文件地址，find -name bdeboot.sh -搜索文件，
* ls -la -查看隐藏文件列表，
* vi/vim .bash_profile -打开文件 
* :set number -显示文件的行号，
* vi/vim .bash_profile -打开文件之后键（i）进行编辑，编辑完成之后键（Esc）退出编辑，:wq! -保存退出，:q! -不保存退出,如果编辑的文件是环境变量的文件例如:.bash_profile保存之后需要刷新一下使其生效：source .bash_profile。
* cat test.txt >> $HOME/.bash_profile 把test中的数据拷到bash_profile文件里面
* echo $JAVA_HOME --查看jdkJAVA_HOME的地址，file test.sh --查看文件编码，ls -lrt --表示按修改时间倒序输出，最新修改的文件会排在下面(没有加 -r 的时候排在上面)，查询文件个数：ls lrt|wc -l,查看文件大小：ls -lh

#### 服务器连接相关命令
* telnet 10.30.9.98 13088 -telnet ip port测试端口是否连通
* ssh khftp@10.0.0.209 -ssh username@ip 连接目标服务器
* sqlpuls username/password@ServiceName -sqlpuls连接oracle数据库

