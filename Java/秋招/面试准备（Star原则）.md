# AbstractMethodError

1. Situation（情境）：在Apusic V10服务器嵌入版上出现**AbstractMethodError**异常。这个异常通常表示在调用方法时，找不到对应的抽象方法的实现。
2. Task（任务）：解决 AbstractMethodError 异常，确保程序可以正常执行。
3. Action（行动）：
   - 首先，查看控制台输出文件，找到异常出现在`com.apusic.aas.websocket.server.WsSessionListener`类的`sessionCreated`方法中。
   - 其次，检查代码实现，判断问题出现的原因是由于应用优先加载了自带的servlet-api，而在实现`WsSessionListener`这个接口类时并没有实现其中的`sessionCreated`方法导致报错。
   - 最后，提出两个修复方案：1.在应用服务器的`WsSessionListener`类中的`sessionCreated`方法前加上关键字`defalut`，这样的话jdk会默认实现这个方法。2.在实现`WsSessionListener`时实现`sessionCreated`方法即可。
4. Result（结果）：经过以上行动，AbstractMethodError 异常问题被解决，程序可以正常执行，不再抛出该异常。

# NullPointerException

1. Situation（情境）：启动Apusic V9服务器后，在设置服务器日志按日输出时出现**NullPointerException**异常。这个异常通常表示调用的属性或者方法为空。
2. Task（任务）：解决 NullPointerException 异常，确保服务器能够正常地按日输出日志。
3. Action（行动）：
   - 首先，查看全局日志文件，找到异常大致出现在`com.apusic.logging.DateFileHandler`类中。
   - 其次，配置idea结合脚本文件进行远程调试，定位到异常发生在`DateFileHandler`类中的`logCalender`未被赋值。
   - 最后，在使用前对`DateFileHandler`中的`logCalender`属性赋上初始值（采用`logCalendar = Calendar.getInstance();`）。
4. Result（结果）：经过以上行动，AbstractMethodError 异常问题被解决，程序可以正常执行，不再抛出该异常。

# Apusic服务器的WebSocket功能发送消息时消息丢失，而tomcat服务起正常运行

1. Situation（情境）：使用Apusic服务器发送消息时消息丢失：单节点访问时域名的地址消息丢失；域名访问单节点的地址信息丢失；单服务器访问单服务器多数情况下正常。
2. Task（任务）：解决Apusic v9服务器上Websocket消息丢失问题。 
3. Action（行动）：
   - 本地撰写Websocket消息应用，在Apusic v9服务器上进行部署，尝试重现错误。 
   - 远程控制客户电脑使用wireshark抓取消息发送成功和消息发送失败时产生的报文。
   - 尝试使用在客户的服务器上安装wireshark从而抓取服务器的网络包进行分析。
   - 尝试使用tcpdump抓取服务器上的网络包进行分析。
   - 尝试分析应用源码，应用websocket源码入口`cn.dreamit.p1000.websocket.servletmode.ServletModeWebSocketServer`，
4. Result（结果）：
   - Websocket应用本地部署成功，但是无法重现错误。  
   - 抓取的两份报文并无明显差异。
   - 由于客户的服务器无法联网安装wireshark，源码安装的话有些依赖同样需要联网进行安装。
   - 客户的麒麟v10服务器是基于Ubuntu 18.04搭建的，因此安装[libpcap-1.10.4](https://www.tcpdump.org/release/libpcap-1.10.4.tar.gz)和[tcpdump-4.99.4](https://www.tcpdump.org/release/tcpdump-4.99.4.tar.gz)，安装步骤[在这](https://www.cnblogs.com/xujiecnblogs/p/17296502.html)。出现错误`./configure - Neither flex nor lex was found. `，通过查阅Github开源仓库中的[issues737](https://github.com/the-tcpdump-group/libpcap/issues/733)得到对应的解决方案，但是由于客户机器无法安装相应依赖，该方式失效。



# AAS应用部署失败时出现java.lang.ArrayStoreException异常

1. Situation（情境）：使用Apusic v9服务器部署应用过程中出现异常`java.lang.ArrayStoreException`，导致应用无法正常部署。
2. Task（任务）：解决ArrayStoreException异常，确保服务器能够正常部署应用。
3. Action（行动）：
   - 首先，查看日志文件发现异常的位置在：`sun.reflect.annotation.TypeNotPresentExceptionProxy`。
   - 其次，由于无法进行远程控制和本地构建，通过撰写了一个简单的测试应用进行部署，发现在本地部署应用并没有问题。
   - 然后，猜测可能是函数的参数问题，导致调用了错误的函数，通过在应用正常下进行debug并未发现任何问题，且调用路径与错误大致相同。
   - 随后，在StackOverflow和github上查找相关错误，发现Spring Boot官方对这个问题进行过解释说明，并给出了排错方案，链接[在这](https://github.com/spring-projects/spring-boot/issues/11214)，据相关人士分析该问题为**依赖冲突**问题。
   - 最后，
4. Result（结果）：



# 修改Apusic应用服务器源码后的重新打包问题

1. Situation（情境）：当客户要求对v10或者v11的功能进行新增，修改代码后需要重新编译生成可执行jar包。

2. Task（任务）：将修改后的源码重新打包成jar包。

3. Action（行动）：

   - 根据要求修改源码并在本地进行测试。
   - 以AAMSv10的Spring Boot 2.4.0为例，首先安装产品包中的相关依赖。

   ```bat
   cd AAMS-V10-embed
   mvn initialize -f embed-install.xml
   cd starter-2.4
   mvn install:install-file -Dfile=aams-spring-boot-starter-2.4.0.jar -DgroupId=com.apusic -DartifactId=aams-spring-boot-starter -Dversion=2.4.0 -Dpackaging=jar -DpomFile=aams-spring-boot-starter.pom.xml
   mvn install:install-file -Dfile=aams-spring-boot-websocket-starter-2.4.0.jar -DgroupId=com.apusic -DartifactId=aams-spring-boot-websocket-starter -Dversion=2.4.0 -Dpackaging=jar -DpomFile=aams-spring-boot-websocket-starter.pom.xml
   mvn install:install-file -Dfile=aams-spring-boot-web-starter-2.4.0.jar -DgroupId=com.apusic -DartifactId=aams-spring-boot-web-starter -Dversion=2.4.0 -Dpackaging=jar -DpomFile=aams-spring-boot-web-starter.pom.xml
   ```

   - 随后进入修改后的aams对应版本的starter进行打包。

   - 打包分为两种：一种是打包成单个jar，一种是按照模块打包。

     - 打包成单个jar

       修改好之后使用命令`maven clean package -f pom-all.xml`进行打包，从而生成新的aams-spring-boot-starter-all-2.4.0.jar文件

     - 按模块打包

     打包成单个jar也有两种方式：字节码文件替换，maven打包。

     **字节码文件替换**

     假设修改后的jar包为A.jar，原始jar包为B.jar，步骤如下：	

     ​	首先找到字节码文件：`jar tf A.jar的绝对路径 | find "寻找的class名称"`，解压A.jar，得到新的class文件的路径。

     ​	执行字节码文件替换：`jar uvf B.jar包的绝对路径 需要替换的class文件路径`。

     **Maven打包**

     在pom.xml文件目录下执行`maven clean package`。

   - 将产品包中的jar包替换为新生成的jar包。

4. Result（结果）：成功替换jar包。
