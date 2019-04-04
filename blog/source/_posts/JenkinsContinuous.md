title: 使用Jenkins持续集成
date: 2019-04-02 22:04:13
categories: 工具

tags: [Jenkins]
---

Jenkins是一个非常好用的工具，我们可以用它来帮我们做很多事，如：自动构建、发布测试、持续集成、邮件通知等等很多很多，真是开发者的一个得力帮手。很多同学还不会使用，本文将从零开始，带你半小时上手，轻松搞定繁琐的配置。
<!-- more -->

### 1.安装

Jenkins有很多安装方式，通常windows我们使用msi安装包，linux下则使用rpm

**windows**:傻瓜式操作，不叙述了。通常我们都在自己电脑上用，安装完成直接到启动Jenkins

**linux**：

**1.1安装**

```
上传文件
rpm -ivh jenkins-xxx.noarch.rpm

```

**1.2配置Jenkins**

返回根目录，用find / -name jenkins搜索jenkins相关目录 如：

```
/etc/sysconfig/jenkins
/etc/rc.d/init.d/jenkins
/etc/logrotate.d/jenkins
/var/lib/jenkins
/var/log/jenkins
/var/cache/jenkins
/usr/lib/jenkins

```

配置jdk，在candidates后面加上自己的jdk路径，如下：

```
vi /etc/rc.d/init.d/jenkins
```

```
candidates="
/etc/alternatives/java
/usr/lib/jvm/java-1.8.0/bin/java
/usr/lib/jvm/jre-1.8.0/bin/java
/usr/lib/jvm/java-1.7.0/bin/java
/usr/lib/jvm/jre-1.7.0/bin/java
/usr/bin/java
/usr/local/java/jdk1.8.0_181/bin/java
"
```

**1.3 修改端口**

```
vi /etc/sysconfig/jenkins
```

**1.4 启动Jenkins**

```
service jenkins start
```

问题1： 启动时，可能出现端口错误，Jenkins的默认端口是8080，检查是否有其他程序，例如Tomcat是否占用8080端口

问题2： 出现以下提示，执行命令，重新加载即可：

```
systemctl daemon-reload
```



### 2.启动Jenkins

启动成功后会让你输入默认密码，有文件位置，打开就能看到，一般默认插件都装上，设置一个管理员帐号。重新登录就安装完成了。



### 3.新建任务

这里以新建maven项目做示例，一开始是没办法建maven项目的，我们要先安装插件

##### 3.1 安装插件

我们要用到的插件（有些可能默认已经安装）

```
Maven Integration plugin //maven
Publish Over SSH //重要，ssh
#下面可选
Subversion Plug-in //svn
Git plugin //git
ThinBackup //备份用

```

##### 3.2 新建任务

主页点击“新建”
项目类型：输入项目名称 `demo`，类型选择“构建一个Maven项目”(如果没有此项可选，请检查插件“Maven Integration plugin”是否已安装)； 

##### **3.3 配置任务**

###### 1.配置源码，一般没问题

###### 2.配置定时构建

定时构建或者轮询构建（源码提交定期检测），一般我们选前者

“日程表”格式与 crontab 相似但有细微差别，示例如下：

```

## 每行由 5 个值组成(空格或TAB分隔)，分别表示分(0-59)、时(0-23)、日(1-31)、月(1-12)、周(0-7, 0/7=周日)
## "M,N" 表示M和N；"M-N" 表示范围[M,N]；"M-N/X" 表示范围[M,N]内每隔X；"*/X" 表示整个范围内每隔X
## 前面提到的M/N/X的值都可以用H(意为Hash)代替，散列值起到随机值的效果，且同一项目取值稳定，这对于项目多时分散压力很有用。
H/10  H(0-8)  *  *  1-5   ## 触发时间: 工作日、Hour为0~8按哈希随机、Minute以10为间隔
H/10  H       *  *  0,6,7 ## 触发时间: 周末、Hour为全天按哈希随机、Minute以10为间隔
## “日程表”修改后，下方会给出下次执行时间点的预告

##下面是一些例子

每天凌晨2:00跑一次 
H 2  * * *

每隔5分钟构建一次
H/5 * * * *

每两小时构建一次
H H/2 * * *

每天中午12点定时构建一次
H 12 * * *   或0 12 * * *（0这种写法也被H替代了）

每天下午18点前定时构建一次
H 18 * * *
 
每15分钟构建一次
H/15 * * * *   或*/5 * * * *(这种方式已经被第一种替代了，jenkins也不推荐这种写法了)
 
周六到周日，18点-23点，三小时构建一次
H 18-23/3 * * 6-7

```

###### 3.Build

输入Root POM和Goals and options，如`pom.xml`和`clean install`

###### 4.构建后操作

选择Send build artifacts over SSH(要提前去设置好ssh服务器，设置好可以点击测试)

填写SSH服务器相关配置

```
Name:选择服务器
Source files:要上传的文件，这里我只要上传jar，填`xxx/target/*.jar`
Remote directory: 远程文件夹位置，最好先新建好，否则可能报错，如`/home/jenkins/demo`

```

`Exec command`:执行命令,注意的是sh脚本要写上jdk环境变量，因为Jenkins的线程马上就会被服务器杀死而无法获取环境变量

```
cd /home/test/
sh app.sh stop
rm -rf *.jar logs/
cp -r /home/jenkins/test/*.jar ./
sh app.sh start
```

`app.sh`顶部写法，记得`chmod +x app.sh`赋予执行权限

```
#!/bin/bash
export JAVA_HOME=/usr/local/java/jdk1.8.0_181
export JRE_HOME=$JAVA_HOME/jre
PATH=$JAVA_HOME/bin:$PATH
export PATH

```

点击高级，勾上`Flatten files`,这样不会创建没用的目录

附上`tomcat`启动脚本示例

```
#!/bin/bash
export JAVA_HOME=/usr/local/java/jdk1.7.0_79
export JRE_HOME=$JAVA_HOME/jre
PATH=$JAVA_HOME/bin:$PATH

TOMCAT_HOME=`/usr/local/tomcat/consumer/tomcat-8080`
SHUTDOWN=$TOMCAT_HOME/bin/shutdown.sh
STARTTOMCAT=$TOMCAT_HOME/bin/startup.sh
echo "关闭$TOMCAT_HOME"
$SHUTDOWN
sleep 2
PIDS=`ps -ef |grep $TOMCAT_HOME |grep -v 'grep'|awk '{print $2}'`
if [ -z "$PIDS" ]
    then
	echo "STOP SUCCESS!"
    else
	kill -9 $PIDS
	echo "KILLED PID: $PIDS"
fi
sleep 2

#if you need to remove logs
#rm -rf $TOMCAT_HOME/logs/* 
#if you need to remove works
#rm -rf $TOMCAT_HOME/wrok/*
#remove old war
rm -rf $TOMCAT_HOME/webapps/*
#cp your war file
cp -r /home/jenkins/demo/*.war $TOMCAT_HOME/webapps/

echo "start $TOMCAT_HOME"
$STARTTOMCAT
PIDS=`ps -ef |grep $TOMCAT_HOME |grep -v 'grep'|awk '{print $2}'`
echo "STARTED PID: $PIDS"
#tail -f $TOMCAT_HOME/logs/catalina.out
```

授人以鱼不如授人以渔，希望小伙伴们能举一反三！

###### 5.tags标签用法

因为少数人会用到，所以放在后面

**svn用法**

- 在常规设置出勾选参数化构建过程

- 选择List Subversion tags
- Name输入`SVN_TAG`
- Repository URL输入tags地址，如`https://192.168.0.xx/svn/test/tags`
- 选择帐号密码
- 勾选Sort newest first
- 源码管理中Subversion的URl改成`https://192.168.0.xx/svn/test/tags/$SVN_TAG`

**git用法相对简单**

- 安装`git-parameter`插件
- 参数化构建选择Git Parameter
- Name输入`tag`,Default Value输入`origin/master`或者不填
- 源码管理中Git的Branch Specifier值填`${tag}`，与上面对应

###### 6.邮件通知

1. 安装插件Email Extension Plugin（最新版本可能默认集成）

2. 进到系统管理->系统设置

   Jenkins URL：jenkins根据这个URL在邮件中显示图片及链接，如一些构建日志，构建任务的工作区间等等可通过该链接访问

3. 同样在系统设置中，配置Extended E-mail Notification：

   Default Content Type：邮件内容格式，可选Plain text和HTML。

   Default Recipients：默认的收件人列表，用逗号分隔。抄送或密送某个收件人可以在其邮箱前面加上cc:或bcc:。

   Default Subject：默认的邮件标题。【构建通知】：$PROJECT_NAME - $BUILD_STATUS - Build # $BUILD_NUMBER!

   Default Content：默认的邮件内容。这里提供一个模板

   ```
   <!DOCTYPE html>
   <html>
   <head>
   <meta charset="UTF-8">
   <title>${ENV, var="JOB_NAME"}-第${BUILD_NUMBER}次构建日志</title>
   </head>
    
   <body leftmargin="8" marginwidth="0" topmargin="8" marginheight="4"
       offset="0">
       <table width="95%" cellpadding="0" cellspacing="0"
           style="font-size: 11pt; font-family: Tahoma, Arial, Helvetica, sans-serif">
           <tr>
               <td><br />
               <b><font color="#0B610B">构建信息</font></b>
               <hr size="2" width="100%" align="center" /></td>
           </tr>
           <tr>
               <td>
                   <ul>
                       <li>项目名称 ： ${PROJECT_NAME}</li>
                       <li>构建编号 ： 第${BUILD_NUMBER}次构建</li>
                       <li>SVN 版本： ${SVN_REVISION}</li>
                       <li>触发原因： ${CAUSE}</li>
                       <li>构建日志： <a href="${BUILD_URL}console">${BUILD_URL}console</a></li>
                       <li>构建  Url ： <a href="${BUILD_URL}">${BUILD_URL}</a></li>
                       <li>工作目录 ： <a href="${PROJECT_URL}ws">${PROJECT_URL}ws</a></li>
                       <li>项目  Url ： <a href="${PROJECT_URL}">${PROJECT_URL}</a></li>
                   </ul>
               </td>
           </tr>
           <tr>
               <td><b><font color="#0B610B">变更集</font></b>
               <hr size="2" width="100%" align="center" /></td>
           </tr>
           
           <tr>
               <td>${JELLY_SCRIPT,template="html"}<br/>
               <hr size="2" width="100%" align="center" /></td>
           </tr>
          
          
       </table>
   </body>
   </html>
   
   ```

   Enable Debug Mode：开启插件的Debug模式，在日志里能看到更多信息。

   其他配置保持默认即可。

   这里的Default Content Type笔者选择了HTML，因为可以显示更为好看丰富的邮件内容，另外邮件内容中用到了一些Jenkins的内置变量，有兴趣的童鞋可以自行百度。

4. 在构建后添加`Editable Email Notification`，填好相关的内容，要注意需要添加`Triggers`，没有添加Triggers就不会发送通知邮件出来，这是一个触发条件。主要添加的Triggers有三类：Failure-Any，Success，Unstable (Test Failures)，分别对应构建失败，构建成功，构建不稳定时触发发送邮件通知。	



