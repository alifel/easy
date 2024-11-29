# java

## nohup java -jar server-0.0.1-SNAPSHOT.jar &

后台启动jar包，启动后，ctrol+c退出即可，程序仍然在执行

## nohup java -jar your-application.jar > output.log 2>&1 &

后台启动jar包，并把日志输出到output.log文件中

## ps -ef | grep "java"

查看进程id，一般第二列，这里是根据程序的关键字“java”

## kill -9 pid

强制杀死进程，不+ -9不强制

## java相关框架，ide，版本的人群占比

<https://www.jetbrains.com/lp/devecosystem-2023/java/>
