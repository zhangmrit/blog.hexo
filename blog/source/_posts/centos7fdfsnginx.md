title: centos7安装fastdfs并整合nginx
date: 2019-08-01 12:12:36
categories: 工具
tags: [FastDFS,nginx,centos7]
---
准备搞个IM,用FastDFS来存储聊天文件，随便找个教程就开撸，结果悲剧了,深入了解后，虽然复杂，但并不难，下面帮大家踩坑。
<!-- more -->

### 1.FastDFS搭建工具下载
作者的GitHub地址：https://github.com/happyfish100

去作者github下载最新版，注意版本对应

Version 5.11对应的fastdfs-nginx-module的Version 1.20 ** **Version 5.10对应的fastdfs-nginx-module的Version 1.19

之所以在安装前写了这么一段话，是因为这个很重要，版本不对应会给接下来的安装带来各种问题。

注意： 下载的时候要下fastdfs-5.11.zip 这个是没有错误的。如果下载 fastdfs-5.11.tar.gz 编译安装会报错；对于fastdfs-nginx-module-1.20.zip也要下载zip结尾的，不然编译和安装会有问题。

当然可以直接去官网去下载，但是官网上不是最新的。

### 2.安装需要用到的应用与类库
首先按需要安装基础依赖

```
yum install vim wget zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl net-tools unzip
```

### 3.安装libfastcommon

```
[root@localhost FastDFS]# cd libfastcommon-1.0.39
[root@localhost libfastcommon-1.0.39]# ./make.sh && ./make.sh install
```
libfastcommon默认会被安装到/usr/lib64/libfastcommon.so但是FastDFS的主程序却在/usr/local/lib目录下 这个时候我们就要建立一个软链接了，实际上也相当于windows上的快捷方式。


```
ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
```

### 4.安装FastDFS

```
unzip fastdfs-5.11.zip
cd fastdfs-5.11
./make.sh
./make.sh install
```
如果没有报错那么就成功了。安装log中会提示FastDFS安装到了/etc/fdfs目录下。

成功后查看安装目录：
```
ll /etc/fdfs/
-rw-r--r--. 1 root      root       1463 11月  8 12:52 client.conf.sample

-rw-r--r--. 1 root      root       7938 11月  8 14:55 storage.conf.sample

-rw-r--r--. 1 root      root       7397 11月  8 12:36 tracker.conf.sample
```
我们需要把这三个示例文件复制一份，去掉.sample
```
cp client.conf.sample client.conf
cp storage.conf.sample storage.conf
cp tracker.conf.sample tracker.conf
```


### 5.安装tracker
##### 创建tracker工作目录
这个目录可以自定义，用来保存tracker的data和log 
根据个人习惯，我创建了下面的目录：

```
mkdir /home/fastdfs_tracker
```
##### 配置tracker

```
vim /etc/fdfs/tracker.conf
打开后重点关注下面4个配置： 
1.disabled=false 
2.port=22122 #默认端口号 
3.base_path=/home/fastdfs_tracker #我刚刚创建的目录 
4.http.server_port=8080 #默认端口是8080
5.bind_addr= 0.0.0.0 监听地址
```
##### 启动tracker

保存配置后启动tracker，命令如下：


```
service fdfs_trackerd start
```
成功后应该可以看到：
```
Starting fdfs_trackerd (via systemctl): [ OK ]
```
进行刚刚创建的tracker目录，发现目录中多了data和log两个目录
```
ll /home/fastdfs_tracker/
drwxr-xr-x. 2 root root 4096 11月  8 15:01 data
drwxr-xr-x. 2 root root 4096 11月  8 12:37 logs
```
最后我们需要给tracker加入开机启动
```
echo "service fdfs_trackerd start" |tee -a /etc/rc.d/rc.local
```
查看一下tracker的端口监听情况
```
netstat -unltp|grep fdfs
tcp        0      0 0.0.0.0:22122           0.0.0.0:*               LISTEN      70382/fdfs_trackerd 
```
端口22122成功监听。

### 6.安装storage
storage的安装与tracker很类似。
##### 为storage配置工作目录
与tracker不同的是，由于storage还需要一个目录用来存储数据，所以我另外多建了两个目录`fastdfs_storage_data`,`fastdfs_storage`
下面是我的目录结构：
```
[root@localhost upload]# ll /home/
总用量 0
drwxr-xr-x. 4 root root  30 8月   1 10:54 fastdfs_storage
drwxr-xr-x. 3 root root  18 8月   1 10:54 fastdfs_storage_data
drwxr-xr-x. 4 root root  30 8月   1 10:46 fastdfs_tracker
```
##### 修改storage配置文件

```
vi /etc/fdfs/storage.conf
1.disabled=false 
2.group_name=group1 #组名，根据实际情况修改 
3.port=23000 #设置storage的端口号，默认是23000，同一个组的storage端口号必须一致 
4.base_path=/home/fastdfs_storage #设置storage数据文件和日志目录 
5.store_path_count=1 #存储路径个数，需要和store_path个数匹配 
6.store_path0=/home/fastdfs_storage_data #实际文件存储路径 
7.tracker_server=172.20.132.57:22122 #我CentOS7的ip地址 
8.http.server_port=8888 #设置 http 端口号
```
修改保存后创建软引用
```
ln -s /usr/bin/fdfs_storaged /usr/local/bin
```
##### 启动storage

```
service fdfs_storaged start
Starting fdfs_storaged (via systemctl): [ OK ]
```
同样的，设置开机启动： 修改rc.local
```
echo "service fdfs_storaged start" |tee -a /etc/rc.d/rc.local
```

查看一下服务是否启动

```
netstat -unltp | grep fdfs 
tcp 0 0 0.0.0.0:22122 0.0.0.0:* LISTEN 2231/fdfs_trackerd
tcp 0 0 0.0.0.0:23000 0.0.0.0:* LISTEN 2323/fdfs_storaged
```
##### 校验整合
到这里，fastdfs的东西都已安装完成，最后我们还要确定一下，storage是否注册到了tracker中去。 
查看命令：


```
/usr/bin/fdfs_monitor /etc/fdfs/storage.conf
```

成功后可以看到： 

```
ip_addr = 172.20.132.57 (localhost.localdomain) ACTIVE
```
### 7.测试
##### 7.1配置客户端
同样的，需要修改客户端的配置文件：
```
vi /etc/fdfs/client.conf
base_path=/home/fastdfs_tracker #tracker服务器文件路径
tracker_server=172.20.132.57:22122 #tracker服务器IP地址和端口号
http.tracker_server_port=8080 # tracker 服务器的 http端口号，必须和tracker的设置对应起来
```
##### 7.2模拟上传
确定图片位置后，我们输入上传图片命令：
```
/usr/bin/fdfs_upload_file  /etc/fdfs/client.conf  /root/1.jpg  #这后面放的是图片的位置
```
成功后会返回图片的路径：
```
/usr/bin/fdfs_upload_file  /etc/fdfs/client.conf  /root/2.png
group1/M00/00/00/rBSEOVoC2QCAJVdoAAFeVJMpopw987.png
```
可以查看文件真实路径

```
ls /home/fastdfs_storage_data/data/00/00/
wKhUhF1CVSqAQLUHAAGuTZ1x9Eo70.jpeg
```
果然通过刚刚返回的路径，我们成功找到了图片。

我们仔细看一下，实际文件存储路径下有创建好的多级目录。data下有256个1级目录，每级目录下又有256个2级子目录，总共65536个文件，新写的文件会以hash的方式被路由到其中某个子目录下，然后将文件数据直接作为一个本地文件存储到该目录中。

###### 如果要访问刚上传的图片，我们得需要结合nginx来实现
###### HTTP请求不能访问文件的原因
我们在使用FastDFS部署一个分布式文件系统的时候，通过FastDFS的客户端API来进行文件的上传、下载、删除等操作。同时通过FastDFS的HTTP服务器来提供HTTP服务。但是FastDFS的HTTP服务较为简单，无法提供负载均衡等高性能的服务，所以FastDFS的开发者——淘宝的架构师余庆同学，为我们提供了Nginx上使用的FastDFS模块（也可以叫FastDFS的Nginx模块）。 
FastDFS通过Tracker服务器,将文件放在Storage服务器存储,但是同组之间的服务器需要复制文件,有延迟的问题.假设Tracker服务器将文件上传到了172.20.132.57,文件ID已经返回客户端,这时,后台会将这个文件复制到172.20.132.57,如果复制没有完成,客户端就用这个ID在172.20.132.57取文件,肯定会出现错误。这个fastdfs-nginx-module可以重定向连接到源服务器取文件,避免客户端由于复制延迟的问题,出现错误。 
正是这样，FastDFS需要结合nginx，所以取消原来对HTTP的直接支持。

### 8.整合nginx
##### 8.1 安装nginx
解压nginx和fastdfs-nginx-module-1.20

```
./configure --prefix=/usr/local/nginx --add-module=/home/upload/fastdfs-nginx-module-1.20/src/

make
make install
/usr/local/nginx/sbin/nginx 
/usr/local/nginx/sbin/nginx -s stop
/usr/local/nginx/sbin/nginx -s reload
```
如果nginx版本过高可能会出现`common_define.h:没有那个文件或目录`错误

```
vim fastdfs-nginx-module-1.20/src/config
ngx_module_incs="/usr/include/fastdfs /usr/include/fastcommon/"
CORE_INCS="$CORE_INCS /usr/include/fastdfs /usr/include/fastcommon/"
```
然后保存后重新编译nginx就可以了
##### 8.2配置storage nginx
修改nginx.conf:

修改监听端口 listen 9991， 新增location

```
server {
        listen       9991;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }

        location ~/group1/M00 {
            root /home/fastdfs_storage/data;
            ngx_fastdfs_module;
        }

        location = /50x.html {
            root   html;
        }
}
```
然后进入FastDFS安装时的解压过的目录，将http.conf和mime.types拷贝到/etc/fdfs目录下：
```
cd fastdfs-5.11/src/conf
cp http.conf  mime.types  /etc/fdfs/
```
另外还需要把fastdfs-nginx-module安装目录中src目录下的mod_fastdfs.conf也拷贝到/etc/fdfs目录下：

```
cp mod_fastdfs.conf  /etc/fdfs/
```
对刚刚拷贝的mod_fastdfs.conf文件进行修改：
```
vi /etc/fdfs/mod_fastdfs.conf
base_path=/home/fastdfs_storage  #保存日志目录
tracker_server=172.20.132.57:22122 #tracker服务器的IP地址以及端口号
storage_server_port=23000 #storage服务器的端口号
url_have_group_name = true #文件 url 中是否有 group 名
store_path0=/home/fastdfs_storage_data   #存储路径
group_count = 1 #设置组的个数
```
在文件的最后，设置group
```
[group1]
group_name=group1
storage_server_port=23000
store_path_count=1
store_path0=/home/fastdfs_storage_data
```
创建M00至storage存储目录的符号连接：
```
ln  -s  /home/fastdfs_storage_data/data/ /home/fastdfs_storage_data/data/M00
```
启动nginx:
```
/usr/local/nginx/sbin/nginx
curl localhost:9991
```
如果出现Welcome to nginx!表示启动成功

##### 8.3配置tracker nginx
在nginx.conf里在添加一个虚拟主机
```
    upstream fdfs_group1 {
        server 127.0.0.1:9991;
    }
   server {
        listen       80;
        server_name  localhost;
        
        location /group1/M00 {
            proxy_pass http://fdfs_group1;
        }
    }
```
重启nginx

```
/usr/local/nginx/sbin/nginx -s reload
ngx_http_fastdfs_set pid=8141
```
##### 8.6HTTP测试
浏览器中输入你刚才上传得到的地址或者重新上传一次

```
http://192.168.84.132/group1/M00/00/00/wKhUhF1CVSqAQLUHAAGuTZ1x9Eo70.jpeg
```

如果显示图片表示配置成功

