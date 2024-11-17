# git

### 1. 原理

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.m0nuhrqi.webp)



### 2. git 常用命令

|                         命令名称                         |                  作用                   |
| :------------------------------------------------------: | :-------------------------------------: |
|           git config --global user.name 用户名           |              设置用户签名               |
|           git config --global user.email 邮箱            |              设置用户邮箱               |
|            <font color="red">git init</font>             |  <font color="red">初始化本地库</font>  |
|           <font color="red">git status</font>            | <font color="red">查看本地库状态</font> |
|         <font color="red">git add 文件名</font>          |  <font color="red">添加到暂存区</font>  |
| <font color="red">git commit -m "日志信息" 文件名</font> |  <font color="red">提交到本地库</font>  |
|           <font color="red">git reflog</font>            |  <font color="red">查看历史记录</font>  |
|     <font color="red">git reset --hard 版本号</font>     |    <font color="red">版本穿梭</font>    |

> 设置用户签名和邮箱与登录github的账户没有任何关系，只是为了表示本人

> 初始化本地库会生成 .git 文件夹，将这个目录交给 git 管理

> 输入 git status 会显示出三行日志
>
> On branch main // 表示在那个分支
>
> Your branch is up to date with 'origin/main'. // 更新状态
>
> nothing to commit, working tree clean // 缓存区很干净

> git reset --hard 版本号 实现的版本穿梭是改变本地工作区



##### 分支：

> 这个分支是在本地上的，是为了本地代码管理

|     **命令名称**     |           **作用**           |
| :------------------: | :--------------------------: |
|  git branch 分支名   |           创建分支           |
|    git branch -v     |           查看分支           |
| git branch -d 分支名 |           删除分支           |
| git checkout 分支名  |           切换分支           |
|   git merge 分支名   | 把指定的分支合并到当前分支上 |



### 3. 推送到 github 上

|                     命令名称                      |                             作用                             |
| :-----------------------------------------------: | :----------------------------------------------------------: |
|                   git remote -v                   |                      查看当前所有远程库                      |
|           git remote add 别名 远程地址            |                      为远程库起一个别名                      |
|              git remote remove 别名               |                       删除远程库的别名                       |
| <font color="red">git push 别名 远程分支名</font> | <font color="red">将代码推送到别名对应的远程库的分支中</font> |
|    <font color="red">git clone 远程地址</font>    |     <font color="red">将远程仓库的内容克隆到本地</font>      |
| <font color="red">git pull 别名 远程分支名</font> | <font color="red">将远程仓库对于分支最新内容拉下来后与当前本地分支<br />直接合并</font> |

> clone 会自己做三件事：1. 拉取代码  2. 初始化本地库  3. 创建别名





对于网络不稳定可以切换SSH连接：

```text
git remote set-url origin git@github.com:ChengHaoRan666/your-repo.git
```

origin 是库的别名，可以通过`git remote -v`查看，`ChengHaoRan666`是用户名，`your-repo`是仓库名





### 4. github 上添加同伴

1. 在项目的设置中找到 collaborators 添加伙伴

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.5j454pkztw.webp)

2. 点击邀请邀请伙伴
 ![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.7zqdjmt99s.webp)

> <font color="red">添加伙伴后伙伴可以将代码提交到远程库中</font>



### 5. 团队外人员远程修改提交代码

1. 先 fork 一个自己版本的项目到自己项目库中

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.9kg4j42nbw.webp)

2. 在自己项目中修改

3. 在原项目中点击pull Request 

    ![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.58hbbkmgti.webp)

