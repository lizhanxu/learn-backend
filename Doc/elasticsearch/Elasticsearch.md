# Elasticsearch

## 常用命令

### 索引操作

```
http://127.0.0.1:9200/_cat/indices?v

GET kb_index

GET kb_index/_settings
```

### 查询操作

```
GET kb_index/_search
{
   "query":{
      "term":{"metadata.file_id":"1f14febc0e3c446a928e3ce1df242ae7"}
   }
}

GET kb_index/_search
{
   "query":{
      "term":{"metadata.title":"first_page"}
   }
}
```



