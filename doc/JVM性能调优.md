#### jvm内存使用情况查看相关

              jvm
              jps：查看本机java进程信息
              jps -l:当前应用程序的完整路径名
              jps -v： 输出传递给JVM的参数
              jstack [pid]：主要用于生成指定进程当前时刻的线程快照
              jmap -heap pid:查看堆使用情况
              jstat:主要是对java应用程序的资源和性能进行实时的命令行监控
              JConsole:可视化监控工具
