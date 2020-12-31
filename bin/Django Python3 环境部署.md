##Python3 & DjangoBlog 环境部署

### CentOS 环境

```shell
yum update

[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# adduser server
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# passwd server
[U7t6f5_n3]
```

### Mysql 环境

```shell

# wget https://repo.mysql.com//mysql80-community-release-el8-1.noarch.rpm

# rpm -ivh mysql80-community-release-el8-1.noarch.rpm

# yum install mysql-server

# yum install mysql-devel

检查是否已经设置为开机启动MySQL服务
# systemctl list-unit-files|grep mysqld

# systemctl enable mysqld.service   #设置开机启动

# systemctl list-unit-files|grep mysqld

# systemctl start mysqld.service #启动服务

# service mysqld status|start|stop

# sudo mysql_secure_installation

# 重启 mysql (U7t6f5_n3)

```



修改 my.cnf

```shell
vi /etc/my.cnf
```



```properties
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci

# 以下是对于低配置情况下的 mysql配置
performance_schema_max_table_instances=400 #设置效果不明显
table_definition_cache=400
performance_schema=off #效果明显
table_open_cache=64
innodb_buffer_pool_chunk_size=64M #效果不明显
innodb_buffer_pool_size=64M #效果不明显

[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4
```

重启 mysql 

```shell
service mysqld restart
```

创建mysql 环境

```sql
CREATE USER 'djangoblog'@'localhost' IDENTIFIED BY '!Djangoblog12345';
CREATE DATABASE `djangioblog`;
GRANT all ON djangoblog.* TO 'djangoblog'@'localhost';
FLUSH PRIVILEGES;
```





### Python3 环境

```shell
第一步：必备的插件：
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# yum install -y update
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# yum install gcc gcc-c++
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# yum -y install gcc automake autoconf libtool make
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# yum groupinstall -y 'Development Tools'
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# yum install -y gcc openssl-devel bzip2-devel libffi-devel

第二步 下载和编译python3.9.0
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# wget https://www.python.org/ftp/python/3.9.0/Python-3.9.0.tgz
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# tar -zxf ./Python-3.9.0.tgz && cd Python-3.9.0/
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# ./configure prefix=/usr/local/python3 --enable-optimizations
此过程可能会出现一些卡顿
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# make && make install

更新 python3软连接
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# rm -rf /usr/bin/python3
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# ln -s /usr/local/bin/python3/bin/python3 /usr/bin/python3

更新 pip3软连接
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# rm -rf /usr/bin/pip3
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# rm -rf /usr/local/bin/pip3
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3
```

### DjangoBlog 运行的虚拟环境

```shell

[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# mkdir -p /data/python/env
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# chown -R server /data/python

以下切换至 server 用户进行操作
[root@iZbp1gy4i7kpu4x4t8sb1uZ ~]# su server
[server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ pip3 install virtualenv
[server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ cd python/env
[server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ virtualenv -p /usr/bin/python3 djangoblog
[server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ source djangoblog/bin/activate
```



### DjangoBlog 环境

通过 `git clone --bare `  XXX 从源仓库中下载

再通过` git push --mirror `推到一个新仓库上。

```shell
su server
[server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ cd /data/python
[server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ git clone https://github.com/Wagfy/DjangoBlog.git
```

安装DjangoBlog所需类库

```shell
[server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ source /data/python/env/djangoblog/bin/activate
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ pip3 install mysqlclient
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ pip3 install --upgrade pip
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ python]$ cd /data/python/DjangoBlog/
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ pip3 install -Ur requirements.txt
# 下载安装需要一段时间，淡定。如果安装过程中出现问题，建议通过pip3 install XXX，单独进行安装

```

DjangoBlog 初始化

```shell
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ ./manage.py makemigrations
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ ./manage.py migrate
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ ./manage.py createsuperuser #创建超级用户
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ ./manage.py collectstatic --no-input
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ ./manage.py compress --force
```



### Gunicorn 配置

gunicorn 是 WSGI HTTP SERVER ，可以直接进行启停，最好配合SuperVisor进行状态监控，其前端放置 HTTP PROXY SERVER 进行代理，如 NGINX。

关于 GUNICORN 的详细介绍 请参考：https://docs.gunicorn.org/en/stable/

安装处理

```shell
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ pip3 install gunicorn
```

直接使用 gunicorn 命令启动服务

```shell
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ gunicorn DjangoBlog.wsgi:application --bind 0.0.0.0:8000
# 如确定为后台启动 使用 nohup gunicorn DjangoBlog.wsgi:application --bind 0.0.0.0:8000 >/dev/null 2>&1 &
# 如下提示信息
[2020-12-31 13:45:30 +0800] [179928] [INFO] Starting gunicorn 20.0.4
[2020-12-31 13:45:30 +0800] [179928] [INFO] Listening at: http://0.0.0.0:8000 (179928)
[2020-12-31 13:45:30 +0800] [179928] [INFO] Using worker: sync
[2020-12-31 13:45:30 +0800] [179929] [INFO] Booting worker with pid: 179929
```



编写gunicorn_start.sh 启动脚本 ，代处理...

```shell
(djangoblog) [server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ deactivate
[server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ vi /data/python/gunicorn_start.sh
```



```shell
#!/bin/bash
NAME='DjangoBlog' #应用的名称
DJANGODIR=/data/python/DjangoBlog #django项目的目录
SOCKFILE=/data/python/DjangoBlog/gunicorn.sock #使用这个sock来通信
USER=server #运行此应用的用户
NUM_WORKERS=3 #gunicorn使用的工作进程数
DJANGO_SETTINGS_MODULE=DjangoBlog.settings #django的配置文件
DJANGO_WSGI_MODULE=DjangoBlog.wsgi #wsgi模块
LOG_DIR=/data/python/DjangoBlog/logs #日志目录

echo "starting $NAME as `whoami`"

#激活python虚拟运行环境
cd $DJANGODIR
source /data/python/env/djangoblog/bin/activate
export DJANGO_SETTINGS_MODULE=$DJANGO_SETTINGS_MODULE
export PYTHONPATH=$DJANGODIR:$PYTHONPATH

#如果gunicorn.sock所在目录不存在则创建
RUNDIR=$(dirname $SOCKFILE)
test -d $RUNDIR || mkdir -p $RUNDIR

#启动Django

exec  /data/python/env/djangoblog/bin/gunicorn ${DJANGO_WSGI_MODULE}:application \
    --name $NAME \
    --workers $NUM_WORKERS \
    --user=$USER \
    --log-level=debug \
    --bind=unix:$SOCKFILE \
    --access-logfile=${LOG_DIR}/gunicorn_access.log
```

```shell
# 注意之前的窗口不要关闭
[server@iZbp1gy4i7kpu4x4t8sb1uZ DjangoBlog]$ /data/python/gunicorn_start.sh
# 如下启动
[2020-12-31 13:53:48 +0800] [179985] [INFO] Listening at: unix:/data/python/DjangoBlog/gunicorn.sock (179985)
[2020-12-31 13:53:48 +0800] [179985] [INFO] Using worker: sync
[2020-12-31 13:53:48 +0800] [179991] [INFO] Booting worker with pid: 179991
[2020-12-31 13:53:48 +0800] [179992] [INFO] Booting worker with pid: 179992
[2020-12-31 13:53:49 +0800] [179993] [INFO] Booting worker with pid: 179993
```

注意，这里的 <font color=red>unix:/data/python/DjangoBlog/gunicorn.sock</font>

在后面搭建nginx 环境时，可以创建 upstream XXX ，对应的 server 即指向上面的红色字部分。在http_proxy 中，指定 upstream，进行反向代理。

>基于 Gunicorn + Nginx 的方式，需要满足三个配置
>
>* 1.使用 gunicorn *.WSGI 的方式在后台启动
>* 2.启动 gunicorn_start.sh ，进行监听
>* 3.更新 nginx 反向代理配置中的 upstream 设置
>
>问题：这样处理的意义（优势）何在？ 尤其是第2步，感觉完全没有必要。





### 配置域名及指向

AliYun 账户 （支付宝登录）

增加域名指向  blog.forbesdoc.com  A记录 121.196.150.212

开通安全组策略 增加8000端口

#### 安装Nginx

```shell
yum -y install nginx
vi /etc/nginx/nginx.conf
# 调整 web端口，浏览器访问测试，页面展示；

```

#### 配置域名 blog.forbesdoc.com

```nginx
server {
    listen 80;
    server_name blog.forbesdoc.com;
    root /data/python/DjangoBlog/;
    access_log /var/log/nginx/blog_forbesdoc_com_access.log;
    error_log /var/log/nginx/blog_forbesdoc_com_error.log;
    location /static/ {
        alias /data/python/DjangoBlog/collectedstatic/;
        expires max;
        access_log        off;
        log_not_found     off;
    }
    location /media {
        # 静态文件配置
        alias /data/python/DjangoBlog/uploads/;
        expires max;
    }
    location ~ \.py$ {
        return 403;
    }

    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_redirect off;
        if (!-f $request_filename) {
            proxy_pass http://127.0.0.1:8000;
            break;
        }
    }
}
```

> 由于域名备案未处理。
>
> 后续将 DjangoBlog 的访问端口调整为80，原 nginx.conf 中的端口为81。通过 IP 地址的方式访问web页面。

