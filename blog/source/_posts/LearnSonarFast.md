title: Sonar快速入门
date: 2019-04-10 11:04:13
categories: 工具

tags: [Sonar]
---

SonarQube 是一个用于管理源代码质量开放平台，它可以从多个维度检测代码质量，可以快速的定位代码中潜在的或者明显的 Bug、错误。它支持包括 Java、Python、Php、C/C++、C#、HTML、JavaScript、PL/SQL、Objective C 等二十多种编程语言的代码质量管理与检测。可作为我们日常开发中检测代码质量的重要工具。很多同学还不会使用，本文将从零开始，带你半小时上手，轻松搞定繁琐的配置。
<!-- more -->
## sonarqube
### 下载
[官网下载](https://www.sonarqube.org/)
### sonarqube配置
```
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false
sonar.jdbc.username=root
sonar.jdbc.password=root
sonar.sorceEncoding=UTF-8
sonar.login=admin
sonar.password=admin
```
### 安装插件

```
chinese pack
checkstyle
pdm
findbugs
```

## sonar-scanner
### 下载
[官网下载](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)
### 设置环境变量

```
SONAR_SCANNER=F:\sonar-scanner
Path=%SONAR_SCANNER%\bin
```

### 配置
打开`conf/sonar-scanner.properties`

一般默认值就行,如果不在同一个机器配置`sonar.host.url`，即`sonarqube`的访问地址
### sonnar-scanner代码检查

##### 1. maven配置（推荐）
只需要在全局maven配置中加入以下参数

```
<settings>
    <pluginGroups>
        <pluginGroup>org.sonarsource.scanner.maven</pluginGroup>
    </pluginGroups>
    <profiles>
        <profile>
            <id>sonar</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <!-- Optional URL to server. Default value is http://localhost:9000 -->
                <sonar.host.url>
                  http://myserver:9000
                </sonar.host.url>
            </properties>
        </profile>
     </profiles>
</settings>
```
执行命令

```
mvn clean verify sonar:sonar
  
# In some situation you may want to run sonar:sonar goal as a dedicated step. Be sure to use install as first step for multi-module projects
mvn clean install
mvn sonar:sonar
 
# Specify the version of sonar-maven-plugin instead of using the latest. See also 'How to Fix Version of Maven Plugin' below.
mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar
```


##### 2. 项目中配置
这个比较复杂，建议多看[文档](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)
下面是`ruoyi`多模块设置示例

ruoyi项目根目录新建文件`sonar-project.properties`
```
# must be unique in a given SonarQube instance
sonar.projectKey=ruoyi
# this is the name and version displayed in the SonarQube UI. Was mandatory prior to SonarQube 6.1.
sonar.projectName=ruoyi
sonar.projectVersion=1.0
 
# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
# This property is optional if sonar.modules is set. 
#sonar.sources=.
#sonar.sources=src
sonar.sources=src/main/java
sonar.java.binaries=target/
sonar.language=java
sonar.modules=ruoyi-admin,ruoyi-common,ruoyi-framework,ruoyi-system,ruoyi-app,ruoyi-generator,ruoyi-quartz
# Encoding of the source code. Default is default system encoding
#sonar.sourceEncoding=UTF-8
```

