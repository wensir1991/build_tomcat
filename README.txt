# build_tomcat
基于centos7系统构建tomcat,并整合prometheus监控
#RUN sed -i '2a JAVA_OPTS="-javaagent:/usr/local/jmx/jmx_prometheus_javaagent-0.3.1.jar=8989:/usr/local/jmx/config.yaml"' /usr/local/tomcat/bin/catalina.sh
整合prometheus监控tomcat启动异常，不构建镜像整合prometheus监控tomcat，启动/关闭tomcat异常，这个问题没有解决，还望大神帮忙解决一下！异常如下:

我的tomcat配置监控prometheus监控，在/us/local/tomcat/bin/catalina.sh 中加入
export JAVA_HOME=/usr/local/jdk
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

JAVA_OPTS="-Dfile.encoding=utf-8 -javaagent:/usr/local/tomcat/bin/jmx_prometheus_javaagent-0.3.1.jar=192.168.7
0.134:8989:/usr/local/tomcat/bin/tomcat.yaml"

然后tomcat启动不了了，也停止不了
出现问题如下
[root@docker bin]# ./catalina.sh stop
Using CATALINA_BASE:   /usr/local/tomcat
Using CATALINA_HOME:   /usr/local/tomcat
Using CATALINA_TMPDIR: /usr/local/tomcat/temp
Using JRE_HOME:        /usr/local/jdk
Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
Exception in thread "main" java.lang.reflect.InvocationTargetException
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
 at java.lang.reflect.Method.invoke(Method.java:497)
 at sun.instrument.InstrumentationImpl.loadClassAndStartAgent(InstrumentationImpl.java:386)
 at sun.instrument.InstrumentationImpl.loadClassAndCallPremain(InstrumentationImpl.java:401)
Caused by: java.net.BindException: 地址已在使用
 at sun.nio.ch.Net.bind0(Native Method)
 at sun.nio.ch.Net.bind(Net.java:437)
 at sun.nio.ch.Net.bind(Net.java:429)
 at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
 at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
 at sun.net.httpserver.ServerImpl.bind(ServerImpl.java:133)
 at sun.net.httpserver.HttpServerImpl.bind(HttpServerImpl.java:54)
 at io.prometheus.jmx.shaded.io.prometheus.client.exporter.HTTPServer.<init>(HTTPServer.java:145)
 at io.prometheus.jmx.shaded.io.prometheus.jmx.JavaAgent.premain(JavaAgent.java:49)
 ... 6 more
FATAL ERROR in native method: processing of -javaagent failed
./catalina.sh: 行 524:  2302 已放弃               (吐核)"/usr/local/jdk/bin/java" -Dfile.encoding=utf-8 -javaagent:/usr/local/tomcat/bin/jmx_prometheus_javaagent-0.3.1.jar=192.168.70.134:8989:/usr/local/tomcat/bin/tomcat.yaml -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs="" -classpath "/usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar" -Dcatalina.base="/usr/local/tomcat" -Dcatalina.home="/usr/local/tomcat" -Djava.io.tmpdir="/usr/local/tomcat/temp" org.apache.catalina.startup.Bootstrap stop



我的jdk版本
[root@docker bin]# java -version
java version "1.8.0_45"
Java(TM) SE Runtime Environment (build 1.8.0_45-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.45-b02, mixed mode)

tomcat 版本
[root@docker bin]# ./version.sh 
Using CATALINA_BASE:   /usr/local/tomcat
Using CATALINA_HOME:   /usr/local/tomcat
Using CATALINA_TMPDIR: /usr/local/tomcat/temp
Using JRE_HOME:        /usr/local/jdk
Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar

我就单独启动了tomcat,把/us/local/tomcat/bin/catalina.sh 中JAVA_OPTS="-Dfile.encoding=utf-8 -javaagent:/usr/local/tomcat/bin/jmx_prometheus_javaagent-0.3.1.jar=192.168.7
0.134:8989:/usr/local/tomcat/bin/tomcat.yaml" 注释掉，tomcat启动和停止又正常了
[root@docker bin]# ./catalina.sh stop
Using CATALINA_BASE:   /usr/local/tomcat
Using CATALINA_HOME:   /usr/local/tomcat
Using CATALINA_TMPDIR: /usr/local/tomcat/temp
Using JRE_HOME:        /usr/local/jdk
Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
[root@docker bin]# ./catalina.sh start
Using CATALINA_BASE:   /usr/local/tomcat
Using CATALINA_HOME:   /usr/local/tomcat
Using CATALINA_TMPDIR: /usr/local/tomcat/temp
Using JRE_HOME:        /usr/local/jdk
Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
Tomcat started.
[root@docker bin]# netstat -ntulp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      567/rpcbind         
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      855/sshd            
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1114/master         
tcp6       0      0 127.0.0.1:8005          :::*                    LISTEN      2410/java           
tcp6       0      0 :::8009                 :::*                    LISTEN      2410/java           
tcp6       0      0 :::111                  :::*                    LISTEN      567/rpcbind         
tcp6       0      0 :::8080                 :::*                    LISTEN      2410/java           
tcp6       0      0 :::22                   :::*                    LISTEN      855/sshd            
tcp6       0      0 ::1:25                  :::*                    LISTEN      1114/master         
udp        0      0 127.0.0.1:323           0.0.0.0:*                           580/chronyd         
udp        0      0 0.0.0.0:736             0.0.0.0:*                           567/rpcbind         
udp        0      0 0.0.0.0:68              0.0.0.0:*                           659/dhclient        
udp        0      0 0.0.0.0:111             0.0.0.0:*                           567/rpcbind         
udp6       0      0 ::1:323                 :::*                                580/chronyd         
udp6       0      0 :::736                  :::*                                567/rpcbind         
udp6       0      0 :::111                  :::*                                567/rpcbind    


