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

Exec command:执行命令,注意的是sh脚本要写上jdk环境变量，因为Jenkins的线程马上就会被服务器杀死而无法获取环境变量

```
cd /home/test/
sh app.sh stop
rm -rf *.jar logs/
cp -r /home/jenkins/test/*.jar ./
sh app.sh start
```

app.sh顶部写法

```
#!/bin/bash
export JAVA_HOME=/usr/local/java/jdk1.8.0_181
export JRE_HOME=$JAVA_HOME/jre
PATH=$JAVA_HOME/bin:$PATH
export PATH

```

点击高级，勾上Flatten files,这样不会创建没用的目录

到此基本结束，后续会补充git(svn)的tags标签用法，构建失败（或成攻）发送邮件等功能