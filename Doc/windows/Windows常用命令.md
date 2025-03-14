# Windows常用命令

## ipconfig

```
查看本机网络，可以看到ip、网关、dns服务器等
ipconfig /all
```

## 查看占用端口对应的PID

```
netstat -ano|findstr "8081"
```

## 查看指定PID的进程

```
tasklist|findstr "9088"
```

## 域名解析

```
域名解析判断
nslookup baidu.com

cname解析判断
nslookup
set type=cname
[输入cname值]_B289519E599FA48911123F1D945EE493.suban1.cqxx.com
```

## 查看文件MD5、SHA值

`certutil -hashfile D:\xxx.txt md5`
