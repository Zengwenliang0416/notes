下载：

```bash
curl -O https://arthas.aliyun.com/arthas-boot.jar // 普通下载
java -jar arthas-boot.jar --repo-mirror aliyun --use-http // 阿里云镜像下载
```

启动：

```bash
java -jar arthas-boot.jar
```

启动后会显示当前正在运行的进程：

![image-20230918200435908](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202309182004978.png)

选择进程后按回车即可看到以上界面，成功使用，此时输入`help`可以获取更多的帮助信息。

`dashboard`：查看当前系统的实时数据面板，输入`Q`或者`Ctrl+C`退出。

![image-20230918200830626](https://raw.githubusercontent.com/Zengwenliang0416/md-pic/main/202309182008652.png)