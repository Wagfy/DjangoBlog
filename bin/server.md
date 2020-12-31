121.196.150.212

root / U7t6f5n3

GIT: https://github.com/Wagfy/DjangoBlog
wagfy@vip.sina.com / !U7t6f5_n3

线下访问 ：

http://121.196.150.212


注：未进行域名备案，无法直接用域名访问。

更新：
本地开发，通过 git push origin master 将修改后的功能进行提交

Server中，服务文件目录 /data/python/DjangoBlog

通过 git pull origin master ，从服务器上进行拉取。

拉取后 nohup gunicorn DjangoBlog.wsgi:application --bind 0.0.0.0:8000 >/dev/null 2>&1 &
重启服务

