# Nginx

## 常用命令

### 防火墙

```sh
# 查看已开放端口
firewall-cmd --list-ports

# 重载配置
firewall-cmd --reload

# 开放端口
firewall-cmd --zone=public --permanent --add-port=8080/tcp --add-port=23333/udp

# 重启
systemctl reload firewalld

# 停用
systemctl stop firewalld

# 启用
systemctl start firewalld

# 查看状态
systemctl status firewalld
```

### nginx

```sh
# 查看nginx进程
ps -ef | grep nginx

# 查看端口使用
netstat -tunlp|grep 80

# 启动
./nginx

# 停用
./nginx -s stop

# 重启
./nginx -s reload 重启

# 测试配置
./nginx -t
```

配置阿里云安全组，即放开对应的端口，让其他主机可以访问

## 常用配置

### ssl证书配置

#### pem crt key

> crt和.key分别代表证书和私钥文件,扩展名是按照文件用途来分的。而.pem是一种文件格式, pem文件是文本格式的,其他证书格式还有DER。 所以证书.crt和.key文件可以是PEM格式文件, 也可以是其他证书格式比如DER(二进制格式) 

#### 建议

* 用于证书的443端口和用于访问的80端口，它们两个的server_name都要将有www和没有www的两个域名都加上

  如：`server_name  www.lizhanxu.com lizhanxu.com`

* 转发最好直接写完整的url不要用变量，因为当变量不止有唯一值的时候会产生一些错误

  最好不要：`rewrite ^(.*) https://$server_name$1 permanent;`

  正确：`rewrite ^(.*) https://www.lizhanxu.com permanent;`

#### 配置

```nginx
最外层->http下
server {
	listen 19002 ssl;
	server_name suban.cqxx.com;
	ssl_certificate /etc/ssl/suban.cqxx.com.cer;
	ssl_certificate_key /etc/ssl/suban.cqxx.com.pem;
	ssl_session_timeout 5m;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-RC4-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:RC4-SHA:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!DSS:!PKS:!RC4;
	ssl_prefer_server_ciphers on;
	charset UTF-8;
	client_max_body_size 1000M; # 单次请求的body最大大小
	
	location / {
		root /usr/local/www;
		index index.html index.htm;
	}
	location /uc-api {
		proxy_pass http://localhost:8080;
		proxy_set_header Host $host:$server_port;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto "https";
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_redirect http:// https://;   # http强制转https
        proxy_set_header X-Xss-Protection 1;
        proxy_http_version 1.1;  #  websocket配置 
        proxy_set_header Upgrade $http_upgrade;  #  websocket配置 
        proxy_set_header Connection "upgrade";  #  websocket配置 
	}
}
```

#### stream配置ssl

```nginx
最外层->
stream {
	server {
		listen 19006;
		proxy_connect_timeout 800s;
		proxy_timeout 24h;
		proxy_pass localhost:7205;
	}
	server {
		listen 19004;
		proxy_connect_timeout 800s;
		proxy_timeout 24h;
		proxy_pass 10.78.2.92:7201;
	}
}
```

### TCP代理配置

```nginx
最外层
stream {
    upstream fs-minio {
        server 10.30.108.226:9000;
    }

    server {
        listen 9006;
        proxy_connect_timeout 800s;
		proxy_timeout 24h;
        proxy_pass fs-minio;
    }

    server {
        listen 18888;
        proxy_connect_timeout 800s;
		proxy_timeout 24h;
        proxy_pass 10.30.108.224:9080;
    }
}
```

### 静态目录代理

```nginx
最外层->http->server下
location /static/ {
    alias /data/flyshare/public/;
    autoindex on;   # 可以关闭目录访问
}
```

### 负载配置

```nginx

```

## 日志处理

### 关闭日志

不建议关闭nginx所有日志

#### 关闭静态文件日志

```nginx
location ~ \.(css|js|jpg|png|gif|swf)$ {
     access_log off;
     log_not_found off;
}
```

#### 关闭URL日志

```nginx
location = /xx-api {
     access_log off;
     log_not_found off;
}
```

### 清空日志

```sh
echo "" > /usr/local/nginx/logs/access.log

cat /dev/null > /usr/local/nginx/logs/access.log
```

### 切割日志

cut_logs.sh

```sh
#!/bin/bash
# 先复制原来的错误日志文件，请根据自己实际的日志路径填写
cp /usr/local/nginx/logs/error.log /usr/local/nginx/logs/error-$(date -d "yesterday" +"%Y%m%d").log
# 清空错误日志文件
cat /dev/null > /usr/local/nginx/logs/error.log
# 先复制原来的访问日志
cp /usr/local/nginx/logs/access.log /usr/local/nginx/logs/access-$(date -d "yesterday" +"%Y%m%d").log
# 清空原来的正常访问日志
cat /dev/null > /usr/local/nginx/logs/access.log
# 定期删除七天前的日志文件
find /usr/local/nginx/logs -mtime 7 -type f -name \*.log | xargs rm -rf
```

设置定时任务

```
$ crontab -e
# 输入
0 0  * * * /usr/bin/sh cut_logs.sh #每天的00:00执行日志切分

$ crontab -l #查看定时任务是否添加成功
```

## 常用参数

### proxy_pass

- **如果proxy_pass末尾有斜杠/，proxy_pass不拼接location的路径**
- **如果proxy_pass末尾无斜杠/，proxy_pass会拼接location的路径**

#### proxy_paxy末尾有斜杠

```
location  /api/ {
    proxy_pass http://127.0.0.1:8000/;
}
```

请求地址：`http://localhost/api/test`
转发地址：`http://127.0.0.1:8000/test`

#### proxy_paxy末尾无斜杠

```
location  /api/ {
    proxy_pass http://127.0.0.1:8000;
}
```

请求地址：`http://localhost/api/test`
转发地址：`http://127.0.0.1:8000/api/test`

注：`/api/`和`/api`区别是影响路径的匹配规则，例如`/apiUC`可以被`/api`匹配到，不能被`/api/`匹配到

