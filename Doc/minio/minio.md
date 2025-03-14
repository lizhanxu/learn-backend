## mc常用命令

```
mc alias set minio http://www.wsb360.com:9000 admin AZsx1234Vchat
mc alias set s3 http://10.29.103.177 ADF58DC5BE3A8GHY533A ionwenfdsi223FE5Ddeg

mc ls minio

mc ls minio/chat

mc mirror --overwrite SOURCE TARGET

mc mirror --overwrite minio/chat minio/ecd

mc mirror --overwrite minio/ecd s3/fss-prd/ECD

./mc cp /opt/data/20210912.zip minio/data

./mc rm minio/chat --recursive --force
```

```
ls       列出文件和文件夹。
mb       创建一个存储桶或一个文件夹。
cat      显示文件和对象内容。
pipe     将一个STDIN重定向到一个对象或者文件或者STDOUT。
share    生成用于共享的URL。
cp       拷贝文件和对象。
mirror   给存储桶和文件夹做镜像。
find     基于参数查找文件。
diff     对两个文件夹或者存储桶比较差异。
rm       删除文件和对象。
events   管理对象通知。
watch    监听文件和对象的事件。
policy   管理访问策略。
session  为cp命令管理保存的会话。
config   管理mc配置文件。
update   检查软件更新。
version  输出版本信息。
du       统计大小
```

https://blog.csdn.net/qq_37284798/article/details/132900357
