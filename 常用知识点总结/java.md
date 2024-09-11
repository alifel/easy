# java

## nohup java -jar server-0.0.1-SNAPSHOT.jar &

后台启动jar包，启动后，ctrol+c退出即可，程序仍然在执行

## ps -ef | grep "java"

查看进程id，一般第二列，这里是根据程序的关键字“java”

## kill -9 pid

强制杀死进程，不+ -9不强制
