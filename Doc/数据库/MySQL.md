# MySQL

## 常用命令

### 登录及使用

```
登录
mysql -h 127.0.0.1 -u root -p

查看所有可见数据库
show databases;

选中数据库
use fschat;

查看所有可见表(包括视图)
show tables;

获取用户信息
SELECT User, Host FROM mysql.user;

查看某数据库所有视图
SELECT TABLE_NAME
FROM INFORMATION_SCHEMA.VIEWS
WHERE TABLE_SCHEMA = 'your_database_name';

执行某个sql文件
mysql -h 127.0.0.1 -u root -p ai_chat < /udpate_sql/a.sql
```

### 用户和授权

#### 创建用户

```
CREATE USER 'username'@'host' [IDENTIFIED BY 'password'];
username:要创建的用户名；
host:代表地址；任何地址可以使用%
IDENTIFIED BY 'password':设置密码，如果不写则为空密码

eg:
create user 'fschat'@'%' IDENTIFIED by 'Fschat123';
```

#### 删除用户

```
DROP USER 'username'@'host';
```

#### 设置密码

```
SET PASSWORD FOR 'username'@'host'=PASSWORD('your_password');
```

#### 更改密码

```
alter user 'username'@'host' identified by 'your_password';
```

#### 授权

```
GRANT privileges ON dbName.tableName TO 'username'@'host' [WITH GRANT OPTION];
privileges:用户的操作权限，如select,delete,update等，共14个，all代表所有。
dbname:数据库名，*代表所有
tablename:表名，*代表所有
WITH GRANT OPTION: 被授权的用户可以将他的拥有的权限授给其他用户

eg:
grant all on fschat.* to 'fschat'@'%';
grant all on *.* to 'fschat'@'%';
```

#### 刷新授权

```
flush privileges;  // 刷新授权，使之立即生效
```

#### 查看授权

```
show grants; // 查看当前用户（自己）权限
show grants for fschat@'%'; // 查看其他用户权限
```

#### 撤销授权

```
REVOKE privilege ON dbname.tablename FROM 'username'@'host';

eg:
revoke all on *.* to 'fschat'@'%';
```

## 读取当前时间

```
select now()
select sysdate()
```

## 查询数据库版本

```
select version()
```

## 授权并创建视图

```
grant select on sys_dept to qyyp;

create view sys_user as select * from fschat.sys_user;
```

## 清除注释

### 清除表注释

```
SELECT
	concat(
		'alter table ',
		table_schema,
		'.',
		table_name,
		' comment ''',
		''';'
	) s
FROM
	information_schema. COLUMNS
WHERE
	table_schema = '数据库名称'
GROUP BY
	TABLE_NAME;

```

### 清除字段注释

```
SELECT
	concat(
		'alter table ',
		table_schema,
		'.',
		table_name,
		' modify column ',
		column_name,
		' ',
		column_type,
		' ',

	IF (
		is_nullable = 'YES',

	IF (
		data_type IN ('timestamp'),
		' null ',
		' '
	),
	'not null '
	),

IF (
	column_default IS NULL,
	'',

IF (
	data_type IN ('char', 'varchar')
	OR data_type IN ('date', 'datetime')
	AND column_default != 'CURRENT_TIMESTAMP',
	concat(
	' default ''',
		column_default,
		''''
	),
	concat(
		' default ',

	IF (
		column_default = '',
		'''''',
		column_default
	)
	)
)
),

IF (
	extra IS NULL
	OR extra = '',
	'',
	concat(' ', extra)
),
 ' comment ''',
 ''';'
	) s
FROM
	information_schema. COLUMNS
WHERE
	table_schema = '数据库名称';

```

```
删除 DEFAULT_GENERATED
反引号 `RANK` rank为关键字
`constraint`
```



## 查询所有数据库连接

```
show processlist;
```

## 问题解决记录

MySQL 连接出现 Authentication plugin 'caching_sha2_password' cannot be loaded

解决方法

show databases;

use mysql;

select host,user,plugin from user where user='root';

更改密码插件，将之前用caching_sha2_password的地方更改为mysql_native_password

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

'root'为caching_sha2_password的user，通过查询得出

'%'为caching_sha2_password的host，通过查询得出

'123456'为新密码



```
create user 'ai_chat'@'%' IDENTIFIED by 'xxAI0826j';
grant all on ai_chat.* to 'ai_chat'@'%';


create user 'llm'@'%' IDENTIFIED by 'llm0826|lang';
grant all on llm.* to 'llm'@'%';


grant select on ai_chat.ai_session_log to llm;
grant select on ai_chat.ai_qa_pairs to llm;
```

## 获取数据库所有非空表

```
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'fschat'  -- 替换为你的数据库名称
  AND table_type = 'BASE TABLE'
  AND table_name NOT IN (
      SELECT table_name
      FROM information_schema.tables
      WHERE table_schema = 'fschat'
      AND table_type = 'BASE TABLE'
      AND table_rows = 0
  );
```



```
SELECT CONCAT('TRUNCATE TABLE `', table_name, '`;') 
FROM information_schema.tables 
WHERE table_schema = 'ai_chat';
```



```
TRUNCATE TABLE `ai_app_base`;
TRUNCATE TABLE `ai_app_config`;
TRUNCATE TABLE `ai_base_files_url`;
TRUNCATE TABLE `ai_file_storage`;
TRUNCATE TABLE `ai_governance_record`;
TRUNCATE TABLE `ai_icon`;
TRUNCATE TABLE `ai_knowledge_app`;
TRUNCATE TABLE `ai_knowledge_base`;
TRUNCATE TABLE `ai_knowledge_base_files`;
TRUNCATE TABLE `ai_qa_app`;
TRUNCATE TABLE `ai_qa_base`;
TRUNCATE TABLE `ai_qa_pairs`;
TRUNCATE TABLE `ai_qa_pairs_temp`;
TRUNCATE TABLE `ai_session`;
TRUNCATE TABLE `ai_session_log`;
TRUNCATE TABLE `ai_session_qa_source`;
TRUNCATE TABLE `ai_synonyms_word`;
TRUNCATE TABLE `ai_ur_app`;
TRUNCATE TABLE `ai_user_operation_log`;
TRUNCATE TABLE `ai_user_role`;
TRUNCATE TABLE `ai_words`;
TRUNCATE TABLE `ai_words_sync_log`;
TRUNCATE TABLE `ai_writing_draft`;
TRUNCATE TABLE `ai_writing_draft_files`;
TRUNCATE TABLE `conversation`;
TRUNCATE TABLE `file_doc`;
TRUNCATE TABLE `knowledge_base`;
TRUNCATE TABLE `knowledge_file`;
TRUNCATE TABLE `message`;
TRUNCATE TABLE `summary_chunk`;
```

