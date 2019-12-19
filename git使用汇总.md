
## 分支的三个版本：
- **远程版本库**，如 github.com；
- **远程快照**，使用 `git branch -r` 查看到的 origin/branch-name，相关信息在 `.git/refs/remotes` 中；
- **本地分支**，使用 `git branch` 查看到的分支，相关信息在 `.git/refs/heads` 中。一般本地分支会关联到对应的远程分支；

## 仓库与版本库：
可理解为：**仓库 = 版本库 + 工作区**。版本库即 .git 文件夹内容，版本库以外的都是工作区内容。

## 工作区、暂存区、版本区：
- 工作区：即我们能看到的目录和文件（除了 .git 这个隐藏目录），对代码的修改发生在工作区；
- 暂存区：使用 `git add .` 将工作区的修改转移到暂存区。暂存区是工作区和版本库（分支）的中转站；
- 版本库：使用 `git commit -m` 将暂存区的内容转移到版本库中，并生成提交记录；


## .git 目录概览：
- 几个 `HEAD`：
    - **FETCH_HEAD**：`git fetch` 用到的，记录了远程仓库每个分支的 HEAD 指向；
    - **HEAD**：本地仓库所在的版本；
    - **ORIG_HEAD**：针对一些危险操作（如 git reset --hard），git 记录了上次 HEAD 的值以防回退（如可以执行 git reset --hard ORIG_HEAD 回退刚刚执行的 reset 操作）；
- **config**：配置信息；
- **hooks/**：钩子 shell 脚本；
- **logs/**：操作日志；
- **objects/**：git 对象；
- **refs**：引用；

## 本地仓库与远程仓库：
- git 是分布式版本管理系统，大体上分为**本地仓库** 和 **远程仓库**。一个本地仓库可以关联 0 个或多个远程仓库。最常用的远程仓库默认命名为 origin。
- 创建仓库的几种方式：
    - 基于远程仓库创建本地仓库（clone）：`git clone git@github.com:linvanda/cache2go.git`；
    - 在本地创建一个空的仓库：`mkdir proj & cd proj & git init`；（git init 与 git init --bare 的区别：git init --bare 生成的是裸版本库，不带工作区，相当于 git init 生成的 .git 内容）；
- 本地仓库与远程仓库的关联：
    1. 创建本地仓库：`mkdir proj & cd proj & git init`；
    2. 添加远程仓库关联：`git remote add origin git@github.com:linvanda/cache2go.git`；
    3. 可以添加多个远程仓库关联，再加一个：`git remote add other git@github.com:linvanda/cache2go-other.git`，操作时可指定仓库，如 `git push other` 将推送到 other 仓库；
- 远程仓库源的一些操作：
    - 查看远程源列表：`git remote -v`；
    - 添加远程源： `git remote add origin git@github.com:linvanda/cache2go.git`；
    - 移除远程源：`git remote remove origin`；
    - 重命名远程源：`git remote rename origin new-origin`；

## 基于远程仓库的分支创建本地分支：
1.  `git fetch`：将 **远程仓库**分支 fetch 到本地的远程仓库**快照**中；
2. `git checkout branch-name`：自动基于 `origin/branch-name` 快照创建本地分支并切换到该分支；

## 基于 master 创建本地分支并推送到远程仓库：
1. `git checkout master`：切换到 master 分支；
2. `git pull --rebase`  ：更新 master 分支到最新版本；
3. `git checkout -b branch-name`：基于 master 创建开发分支；
4. `git add . & git commit -m "commit reason"`，提交修改；
5. `git push -u origin branch-name`：将该分支推送到远程并在两者之间创建关联（这样后面就可以直接执行 `git push` 推送分支）；

## git pull 的两种模式：rebase 和 merge
- **merge** 模式：直接执行 `git pull` 默认就是该模式。该模式是将远程分支 `fetch` 下来，然后将其 `merge` 到相应的本地分支，并在 log 中生成一条 commit。使用该模式下，`git log` 查看 commit 会发现大量的 merge origin/branch-name into branch-name 的无意义的合并日志。
- **rebase** 模式：`git pull --rebase`。推荐使用的模式。将远程分支 `fetch` 下来后，将其 `rebase` 到本地分支，相当于“追加”，不会生成 commit。

## 合并分支：
1. `git pull --rebase`，同步当前分支；
1. `git checkout test`，进入 test 分支；
1. `git pull --rebase`，同步 test 分支；
1. `git merge --no-ff feature-order`，将 feature-order 合并到 test 分支；
1. `git push`，推到远程；

## 分支合并的问题：
- 为啥加 `--no-ff`：no ff 是 no fast forward 的缩写。git merge 默认是fast forward（快进） 模式，该模式下不会生成单独的 commit，这对历史查看和回滚都不便。加 --no-ff 则强制生成独立的合并 commit；
- 出现冲突咋办？出现冲突时一定要找冲突相关人一起解决，解决完毕后执行 `git add . & git commit` 即可。如果无法解决冲突，则要撤销此次合并：`git merge --abort`；

## git diff:
- `git diff A B` B相对于A有什么不同。如果不指定B，则B默认是工作区；如果同时不指定A，则A默认是暂存区。
- `git diff` 相当于 git diff stage workspace 。 工作区相对于暂存区 有哪些不同。
- `git diff HEAD` 相当于 git diff HEAD workspace 。 工作区相对于HEAD区 有哪些不同。
- `git diff commitId` 相当于 git diff commitId workspace。 工作区相对于commitId那次提交 有哪些不同。
- `git diff commitIdA commitIdB` commitIdB相对于commitIdA有哪些不同
- `git diff master` 相当于 git diff master workspace： 当前工作区相对于master分支的最近提交有哪些不同。
- `git diff master HEAD` 当前的HEAD相对于master分支最近提交有哪些不同
- `git show commitID` 本次提交相对于上次提交有何不同

## 撤销修改：
- 撤销 workspace 中某个文件的修改：`git checkout < filename >`。
  变体：`git checkout`：撤销工作区所有文件的修改。
- 撤销 stage 中某个文件的修改（将其放入 workspace）：
  `git reset HEAD <filename>`
  变体：`git reset HEAD` 撤销暂存区所有文件修改到工作区
- `git reset --hard HEAD` 撤销修改并丢弃（不放回工作区），   此时工作区内容和HEAD中的相同。

## 合并 commit：
合并本地多次 commit 为一条 commit： 
     1.  `git rebase -i HEAD~4`
     2. 弹出文本，将第二行开始的pick改为s(或squash)
     3. 保存，退出
     4. 推到远程：git push -f

## 对比文件修改历史：
查看某个文件最新代码中每行是由谁修改的（以及在哪次提交中修改的）：
`git blame filename`

## 历史日志 log：
- `git log --grep="test"` 搜索提交信息中包含 test 的
- `git log -S abc` 搜索修改内容包含"abc"的 commit
- `git log --date=relative` 以相对日期格式显示日志（如2 hours ago）
- `git log —oneline` 一行紧凑显示提交id和注释
- `git log --abbrev-commit` 显示短 commit id
- `git log -5` 显示最近5条提交
- `git log —all` 显示所有分支的提交历史（默认只显示当前分支的）
- `git log —graph` 图形显示分支
- `git log --pretty=format:'…'` 格式化显示

## 格式化显示日志：
`git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative`
可设置别名：
`git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative”`

## 删除 untracked files
`git clean -f`
连 untracked 的目录也一起删掉
`git clean -fd`

## cherry-pick
在分支 A 做了些修改并提交后，想把这些修改应用到分支 B，但又不能直接 merge A 到 B，又不想手动 copy，此时可以用 cherry-pick：
```
git checkout B
git cherry-pick commitid-from-A
```

## 撤销合并：
1. 查看合并提交详情：git show mergecommitid，会有如下一行：Merge: 022ff6f b2cd947，其中022ff6f和 b2cd947分别是两个分支合并前最近提交的commitId。
2. 撤销合并时用-m指定需要以谁为准（主干mainline）：
`git revert -m 1  mergecommitid`，这样就回退到 022ff6f身上（丢弃掉被合并分支的一系列提交）。
3. 撤销合并前用 `git log —graph` 能很清晰的看出分支合并关系以及将要撤销掉的提交系列。
4. 一般情况下，我们都是错误合并后需要撤销到合并之前的状态，因而一般都是 `git revert -m 1 …`

## 删除本地分支：
`git branch -D branch-name`

## 删除远程分支：
`git push origin :branch_name`
或
`git push --delete origin branch_name`

## 基于远程分支创建本地分支：
`git checkout -b localbranch origin/remotebranch` 会创建本地分支localbranch并追踪到远程分支。
基于本地分支创建（如基于本地 master）则不会追踪任何远程分支，此时要用 `git push -u origin localbranch:remotebranch` 推送并追踪。

## 提交：
`git commit -am '…'`
-a：add 将未暂存的文件也提交。

## 代码评审：
gerrit
或者gitlab的merge request

## git flow:
介绍：http://nvie.com/posts/a-successful-git-branching-model/
安装： apt-get install git-flow

## 批量删除分支：
- 批量删除本地分支：git branch |grep release|xargs git branch -D （删除本地release分支）
- 批量删除远程分支：git branch -r|awk -F '[/]' '/release/ {printf "%s\n", $2}'|xargs -I {} git push --delete origin {}  （删除远程release分支）

## 从仓库中删除已跟踪的文件（将文件变成untracked，不跟踪）：
- `git rm --cached` 文件名
- `git rm --cached -r ` 目录

## 删除远程不存在的“本地远程分支“：
很多时候其他人删除了远程分支，但我们自己电脑上还有一大堆 origin/... 这些“本地远程分支”，可执行以下指令同步删除：
`git remote prune origin`

## 同时推送到多个源：
`git remote set-url --add --push origin https://github.com/linvanda/wecarswoole.git`
这样 git push 的时候就会 push 到多个源。

## 换行符问题（mac、windows、linux混合开发时可能遇到）
`git config --global core.autocrlf true` （提交时自动将CRLF转成LF，检出时将LF转成CRLF）
`git config --global core.autocrlf input` (提交时自动将CRLF转成LF，检出时不处理(意味着检出时是LF))
（以上两个都是保证git仓库中是LF）
`git config --global core.autocrlf false` (关闭，不处理，是啥就是啥)


## 其它实践问题：
- 永远不要将 test 合并到开发分支；
- 要经常将 master 合并到自己到开发分支；
