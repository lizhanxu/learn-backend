## git使用笔记

[ ]表示可写可不写

< >表示须填写内容

注意：github是靠user.email来判断提交用户的
#### 使用前配置：
|                                            |                           |
| ------------------------------------------ | ------------------------- |
|git config [--global] user.email 'youremail'|设置email,--global表示全局设置|
|git config [--global] user.name 'yourname'  |设置name,--global表示全局设置 |        
|git config --list|查看配置信息，主要是查看 user.name 和 user.email 的值|

#### 常用命令：

|              |                     |
| ------------ | ------------------- |
|**git status**|显示工作目录和暂存区的状态|

|                                            |                              |
| ------------------------------------------ | ---------------------------- |
|git remote|不带参数，列出已经存在的远程分支；-v列出分支的详细信息|
|git remote -v|列出分支的详细信息（地址）|
|git remote add <remote-name> <remote-url>|   添加远程仓库并命名|
连接远程仓库时密码填写错误，可以在windows的凭据管理器中修改

|                                            |                              |
| ------------------------------------------ | ---------------------------- |
|git clone -b <branch-name>  <remote-url>  |       克隆远程仓库的指定分支|
|git clone <remote-url> [<name>]      |    克隆远程仓库     [name]自定义本地仓库名|

|                                            |
| ------------------------------------------ |
|**git clone 须知:**|
|①默认情况下将获得整个远程仓库（所有分支）|
|②初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态|
|③会自动将其添加为远程仓库并默认以 origin 为别写|
|④默认在master分支上|

|                             |                 |
| --------------------------- | --------------- |
|git branch                   |查看分支          |
|git branch <branch-name>     |创建分支          |
|git checkout <branch-name>   |切换分支          |
|git checkout -b <branch-name>|创建+切换分支      |
|git merge <branch-name>      |合并某分支到当前分支|
|git branch -d <branch-name>  |删除分支          |


|                             |                 |
| --------------------------- | --------------- |
|git init|初始化仓库，生成. git子目录。 git init 仅仅是做了一个初始化的操作，项目里的文件还没有被跟踪|



|                             |                 |
| --------------------------- | --------------- |
|git add|添加文件追踪 ，      git add . 表示追踪所有文件|
|**git rm -r --cached <文件夹/文件>**|取消对已追踪文件夹/文件的追踪 , 需看情况给出路径|


|                             |                 |
| --------------------------- | --------------- |
|git commit -m '描述'|将暂存区的代码提交到本地仓库|
|git log |显示本地仓库提交日志 &emsp;&emsp;-p显示每次提交内容的差异 &emsp;&emsp;-2显示最近两次提交|


|                                    |
| ---------------------------------- |
|git push [remote-name] [branch-name]|
|将本地的[branch-name]分支推送到[remote-name]主机的[branch-name]分支。如果[branch-name]不存在，则会被新建.|


#### 解决git国内下载慢的问题
    修改host文件         路径：C:\Windows\System32\drivers\etc
    
    添加：
    151.101.72.249 global-ssl.fastly.Net
    192.30.253.112 github.com
    
    刷新dns：     ipconfig /flushdns

#### commit规范

```
feat: 新功能（feature）
fix: 修补bug
docs: 文档（documentation）
style: 格式（不影响代码运行的变动）
refactor: 重构（即不是新增功能，也不是修改bug的代码变动）
chore: 构建过程或辅助工具的变动
revert: 撤销，版本回退
perf: 性能优化
test：测试
improvement: 改进
build: 打包
ci: 持续集成
```

#### 本地分支关联远程

```
git branch --set-upstream-to=origin/develop develop
```

#### 长期记住账号密码

```
git config --global credential.helper store
```

