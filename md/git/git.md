# git的配置

```
//配置git的用户名和邮箱
git config --global user.name "wanghongsheng"
git config --global user.email "190273416@qq.com"

//查看用户的用户名和邮箱
git config  user.name
git congig  user.email
```

# git 创建版本库

1. 新建一个空文件夹（目录名中不能包含中文名）
2. git cd到文件夹下
3. git init

# git命令

1. git add 文件名

>将文件添加到仓库
>
>git add readme.txt

2. git commit -m "本次提交的说明"

>将文件提交到仓库
>
>git commit  -m "commit"

3. git diff "文件名"

>diff对比 工作区和仓库的不同
>
>git diff readme.txt
>
>git diff HEAD --readme.txt

4. git status 

>查看当前仓库状态
>
>git status

5. git log

>查看版本日志
>
>git log --pretty=online

6. git reset

>回退上一个版本
>
>git reset --hard HEAD^
>
>回退到指定版本
>
>(commit id 可以通过git log查看)
>
>git reset --hard comit id（12345）

