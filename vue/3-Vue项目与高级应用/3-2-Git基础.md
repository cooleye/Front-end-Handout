# Git开发文档
### 版本控制系统（version control system），简称VCS 
## 1.安装
* 官网：https://git-scm.com/
* Mac 通过homebrew安装或者xcode安装
* Windows安装gitbash
* 运行`git`命令查看是否成功

## 2.设置
配置用户名和邮箱
* `git config —global user.name   "你自己的用户名"`
* `git config --global user.email   "你自己的邮箱"`

## 3.创建仓库
创建一个目录，进入到目录中执行 `git init` ,使用命令`ls -la `查看所有文件，看到 *.git* 的文件说明成功创建仓库。

#### 4.把文件添加到仓库
1. 建一个文件放到仓库中，例如readme.txt, 
2. 执行 git add readme.txt
3. 提交 执行 git commit -m ‘第一次提交’   -m是本次提交的注释

## 4. 常用git命令
#### 1. 查看状态
`git status `

#### 2. 删除文件
`git rm file`   
并且    
`git commit -m '注释'`

#### 3. 创建远程仓库
在[github](https://github.com)或者[码云](https://gitee.com)上创建仓库

#### 4. 关联远程仓库
`git remote add origin “仓库地址”`  
添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

#### 5. 删除关联
`git remote rm origin`

#### 6. 把本地仓库内容推送到远程仓库
`git push -u origin master`

#### 7. 克隆远程仓库
`git clone  “远程仓库地址”`

#### 8. 查看提交的历史记录  
 `git log`
 
 `git log --pretty=oneline` 只看版本号

#### 9. 回滚
回到上一个版本  
`git reset --hard HEAD^`    

上上一个本本就是    
`git reset --hard HEAD^^`   

返回第100个版本就是 
`git reset --hard HEAD~100` 

#### 10. 使用版本号回滚制定版本
如果又想回到刚才的版本，只要命令窗口没关掉就可以用 版本号返回，版本号只写前几位就可以了
`git reset --hard 3628164`

#### 11. 查看命令记
如果窗口关掉了，可以用 git reflog 查看命令记录
`git reflog`

#### 12. 创建&切换分支
`git checkout -b dev`

git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
`git branch dev`  //创建分支
`git checkout` // 切换分支

#### 示例：  
`git branch f1` 
`git checkout f1` 切换到f1分支  
`git push origin f1` 推送f1 分支到远程仓库f1分支    
`git push origin master:f1` 推送master 到远程的f1分支  

#### 13. 合并分支 
合并某分支到当前分支，如果要把dev合并到master，则先切换到master，在执行 `git merge dev`

#### 14. 删除分支
`git branch -d dev`

#### 15. 删除远程分支
`git push origin :dev`  
或者    
`git push origin --delete dev`


#### 16. 查看远程分支

`git branch -r`

#### 17. 查看本地和远程分支
`git branch -a`

#### 18. 拉取并merge代码
`git pull`

#### 19. 打标签
https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE
1. 查看标签: `git tag`
2. `git tag -a v0.1 -m 'my version 0.1'`  有附注的标签
3. `git tag v0.3` 轻量级标签 
4. `git push origin v0.1` 推送标签
5. 删除本地标签 `git tag -d v0.2`
6. 删除远程标签 `git push origin --delete v0.2`


## 5. 创建 SSH key
`ssh-keygen   -t   rsa    -C   "你自己的邮箱"`

1. 一路回车就可以
2. 在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥
3. Window: C:/User/Administrator/.ssh
4. Mac: open ~/.ssh  
5. 用编辑器打开 id_rsa.pub

<!--![image](http://note.youdao.com/yws/res/7024/01B96F55375F435DBF2BC8EDB1646ABC)-->
<img  width=240 src="http://note.youdao.com/yws/res/7024/01B96F55375F435DBF2BC8EDB1646ABC"/>
<br>
6. 复制文本内容，登录github->settings->SSH and GPG keys
点击 New SSH Key 按钮：

![image](http://note.youdao.com/yws/res/7021/ED846A36C14B46FE87E0B97F0A0A6E44)

把刚才复制的 id_rsa.pub 公钥内容粘贴到 key的输入框里：
![image](http://note.youdao.com/yws/res/7025/D0E3DEAD591B49DAB9E16A5012A6164D)
title自己随便填。
之后点击 下边的 Add SSH Key 按钮保存即可。
 
## 6. 记住用户名密码
如果不像每次提交都输入用户名密码，可以这么做：
```
git config --global credential.helper wincred
```

## 7. Git团队开发工作流

1. 组长在github上创建组织

2. 在组织中创建项目

3. 发送邮件，邀请团队成员加入

4. 团队成员clone项目到本地  
    `git clone 仓库地址`

5. 在本地从master分支中创建自己的的开发分支     
    `git checkout -b davie`

6. 提交分支到github     
    `git push origin davie`

7. 发起一个pull requests

8. 组长查看pull requests请求，合并分支

9. 团队成员拉取代码到本地master分支          
    `git pull origin master --rebase`

10. 把master分支合并到自己的开发分支        
    ```
    git checkout davie
    git merge master
    ```
    
11. 继续开发，重复步骤6、7


----

## 给成员添加权限

#### 1.   找到组织
![image](http://note.youdao.com/yws/res/7212/E426894C7C0C42BE9A16B0499625C924)

##### 2. 查看团队成员

![image](http://note.youdao.com/yws/res/7216/0490AFBF9FAF42C9A7F8652874EFC3F9)

#### 3. 找到需要添加权限的人

![image](http://note.youdao.com/yws/res/7218/57CBA168CCA345589A66B60ADB7E1F24)

点击 Manage

#### 4. 然后：
![image](http://note.youdao.com/yws/res/7222/3BEBCAAAB51D4FBE89A7CD56D981CAE2)


#### 5. 接下来：
![image](http://note.youdao.com/yws/res/7224/85A29ADE6B3E45FC8A94FFC336D1C55E)

#### 6. 添加 write权限

![image](http://note.youdao.com/yws/res/7227/B9821E60D4FF4015846AEF375C2B1348)


之后，团队成员就可以向仓库中推送代码了。