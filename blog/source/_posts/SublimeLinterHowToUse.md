title: SublimeLinter如何校验html、js、css
date: 2016-02-01 10:03:41
categories: 工具
tags: [前端]
---
SublimeLinter是Sublime的一个代码检测工具插件。但是要正常使用它,还需要安装相应的插件。
<!-- more -->

### Html校验工具
1. 先安装SublimeLinter-contrib-htmlhint,很简单,ctrl+shift+p 直接install;
2. 安装htmlhint,打开cmd;

    `npm install -g htmlhint@latest`

3. 检查安装是否完成;

    `htmlhint --version`

### Js校验工具
1. 先安装SublimeLinter-jshint;
2. 安装jshint;

    `npm install -g jshint`

3. 检查安装是否完成;

    `jshint -v`

### Css校验工具
1. 先安装SublimeLinter-csslint;
2. 安装csslint

    `npm install -g csslint`

3. 检查安装是否完成

    `csslint --version`

好了,这样应该就可以自动校验了.
