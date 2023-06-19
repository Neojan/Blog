## 下载git：
https://git-scm.com/downloads

## 配置git
安装好后开始菜单里找到“Git”->“Git Bash”

在bash里面输入
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"

通过git init命令把这个目录变成Git可以管理的仓库，此时会生成.git的目录
添加文件到Git仓库，分两步：
使用命令git add <file>，注意，可反复多次使用，添加多个文件；
使用命令git commit -m <message>，完成。
git status： 查看工作区状态
git diff： 查看修改内容
git log： 查看提交历史 一些参数 git log --graph --pretty=oneline --abbrev-commit, -n:n是次数
git reset --hard commit_id： 回退到某个版本
git diff HEAD -- <filename>：查看工作区和版本库里面最新版本的区别
git checkout -- <filename>: 减出文件，没有 -- 变成了切换分支
git rm <filename>： 删除一个文件

## 远程仓库搭建：
第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
$ ssh-keygen -t rsa -C "youremail@example.com"
你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可

第2步：登陆GitHub，打开Settings-SSH and GPG Keys页面：
然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容：

添加远程库：点击github右上角"+"号-New Repository，输入名称，勾选initialize，然后Creat即可。

### 本地仓库关联远程仓库:（可以选择ssh和https）
git remote add origin https://github.com/Neojan/Jangit.git 或 git@github.com:Neojan/Blog.git
查看远程库信息：git remote -v。可以看到push pull地址

### 从远程库合并更新到本地：
git pull

### 把本地库的所有内容推送到远程库上: 
git push -u origin master
我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
git push origin master：origin为远程库，master为本地主分支，master换成其他就将其他分支push上去。push之前最好先pull以下。

从远程仓库克隆到本地：（可以选择ssh和https）
git clone https://github.com/Neojan/Blog.git 或 git@github.com:Neojan/Blog.git

### 在本地创建和远程分支对应的分支:
git checkout -b branch-name origin/branch-name

branch: 
git checkout命令加上-b参数表示创建并切换
git checkout -b <name> 
查看分支：git branch
创建分支：git branch <name>
切换分支：git checkout <name>
创建+切换分支：git checkout -b <name>
合并某分支到当前分支：git merge <name>
删除分支：git branch -d <name>，在当前branch无法删除当前branch,
强行删除未合并的分支：git branch -D <name>。

### 合并的Fast-Forward模式
当前分支合并到另一分支时，如果没有分歧解决，就会直接移动文件指针。这个过程叫做fastforward。这样合并不会保留分支修改记录信息。git默认采用这种方式
如日志commit 5314253e655a34b488664fc9a4d0c0005521bf27
合并--no-ff模式
git merge –no-ff <name>
如日志commit 42bd579e9d1884acf43cd67bccd47441b006b7fa (dev)
–no-ff，其作用是：要求git merge即使在fast forward条件下也要产生一个新的merge commit。此处，要求采用–no-ff的方式进行分支合并，其目的在于，希望保持原有“develop branches”整个提交链的完整性。

### git 临时切换branch
加入当前branch有内容未提交，但需要到其他分支修改bug，如果此时切换branch，git会提示
error: Your local changes to the following files would be overwritten by checkout:
        xxxx
Please commit your changes or stash them before you switch branches.
这时候就需要使用 git stash 保存当前branch 修改进度。此时用git status看是没有改动了，就可以正常切换到其他branch，在其他branch操作完，切回来之后，
git stash list ：查看存储的进度列表。
git stash pop [–index] [stash_id]：恢复之前的进度。不带参数默认恢复最新
git stash apply [–index] [stash_id]：不删除之前的进度记录并恢复之前的进度，此时查看进度列表仍有记录。
git stash drop：删除一个存储的进度。默认删除最新。
git stash pop = git stash apply + git stash drop。
git stash clear：删除所有进度记录

git rebase
rebase操作可以把本地未push的分叉提交历史整理成直线；
rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

git tag <name>：在当前提交前打标签
git tag <tagname> <commit id>:给某次提交加标签
git tag -a <tagname> -m "blablabla...":指定标签信息
git show <tagname>：查看tag说明
git tag -d <tagname>：删除标签

.gitignore：忽略文件规则文件 https://github.com/github/gitignore

别名：
git config --global alias.<别名> <原名>
如：
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
每个仓库的Git配置文件都放在.git/config文件中,如果删除可以进配置文件删除

参考：
https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000
