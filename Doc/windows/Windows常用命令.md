# Windows常用命令

## ipconfig

```
查看本机网络，可以看到ip、网关、dns服务器等
ipconfig /all
```

### 刷新DNS缓存

```
ipconfig /flushdns
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

方式一
判断解析是否生效
nslookup
输入记录类型
set type=cname
输入记录值:主机记录+"."+主域名（二级域名）
_B289519E599FA48911123F1D945EE493.suban1.cqxx.com

方式二
nslookup -type=CNAME _243a61334894a1b25605243a55483464.suban.cqxx.com 223.5.5.5
           记录类型     记录值:主机记录+"."+主域名（二级域名）                dns server
nslookup -qt=CNAME _243a61334894a1b25605243a55483464.suban.cqxx.com 223.5.5.5           

```

## 查看文件MD5、SHA值

`certutil -hashfile D:\xxx.txt md5`
