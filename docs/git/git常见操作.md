## 前言

[有趣的 git 分支学习游戏](https://learngitbranching.js.org/)

## 配置

##### 1. 配置文件

- `/etc/gitconfig`
- 说明：系统上每个用户及他们仓库的通用配置
  - 使用：`git config --system`
  - 级别：系统级别「system」
  
- `~/.gitconfig`或`~/.config/git/config`
- 说明：当前用户的所有仓库
  
- 使用：`git config --global`
  - 级别：全局级别「global」
  
- `.git/config`

  - 说明：当前仓库的 git 目录中的 config 文件，针对当前使用的仓库
  - 使用：`git config --local`
  - 级别：仓库级别「local」

##### 2. 配置文件优先级

- `local`>`global`>`system`
- 每一级别的配置都会自动覆盖上级相同的配置，当前项目仓库配置优先于其余配置

##### 3. 常用操作

- 打印信息

  - 所有信息

    `git config --list`

  - 全局信息

    `git config --global --list`

- 用户信息设置

  每次提交时都需要加上用户名与邮箱地址，因此可以设置成全局变量（合理理解配置文件的优先级）;

  设置节点 user，属性分别为 name 和 email

  ```
  git config --global user.name UserName
  git config --global user.email EmailAddress
  ```

- 移除变量

  - 移除单个变量
    `git config --global --unset 属性名`

  - 移除所有变量
    `git config --global --unset-all 属性名`

- 设置文本编辑器

  ```
  # 未设置
  git config --global core.editor vim
  # 已设置，先删除再添加
  git config --global --unset core.editor
  git config --global core.editor vim
  ```

##### 4. 查看配置

- 查看已有配置信息；
  如果有重复变量名，表示不同配置文件的配置，实际上 git 会采用最后一个，也就是优先级最高的那个；

  `git config --list`

- 查看当前用户配置信息

  `cat ~/.gitconfig`

- 查看系统级别配置信息

  `cat /etc/gitconfig`

- 查看某项配置变量值，如用户名

  `git config user.name`

##### 5. 汇总

- git 配置文件格式是什么？

  ini

## 代码克隆、更新

##### 1. 常见克隆

`git clone [仓库地址]`

##### 2. 克隆指定分支代码

`git clone -b [分支名称] [仓库地址]`

##### 3. 更新代码

`git pull [远程主机名，一般为 origin] [远程分支名，一般为 master]`

## 远程关联

##### 1. 工具关联

如：AndroidStudio、idea

1. 开启版本管理「VCS → Enable Version Control...」，选择 git；
2. VCS → Git  →  Remotes，添加远程地址及相关信息；
3. 一般可以用了，有冲突之类的问题就需要做合并之类的操作；

##### 2. 命令关联

1. 初始化项目：`git init`
2. 项目提交本地仓库：`git add .`和`git commit -m [描述信息]`
3. 关联远程主机：`git remote add origin [仓库远程地址]`
4. 合并线上与本地仓库：`git pull --rebase origin master`
5. 推送代码：`git push -u origin master`

## 代码提交

0. `git status`

   查看文件修改状态，一般是检查是否是自己修改的文件，红色表示为自己修改的文件；
   非必需步骤；

1. `git add .`或`git add [文件名]`

   本地修改文件添加至本地 git 缓存区

   - `git add .`：添加全部修改过的文件；
   - `git add [文件名]`：添加单个「特定」文件；

2. ``git commit -m [描述信息]`

   推送缓存区文件至本地 git 库

3. `git push [远程主机名，一般为 origin] [远程分支名，一般为 master]`

   推送本地 git 库文件至远程主机

说明：远程主机名「`remote`」与远程分支名「`branch`」都可以在当前仓库配置文件「`.git/config`」中查看

## 代码合并

##### 1. 分支合并

将 `master` 分支合并到`develop`分支

```
git checkout develop
git merge master
# 或者一行代码
git merge develop master
```

##### 2. 部分合并

- 合并部分提交

  `git cherry-pick [commit-id]`

- 合并部分文件

  `master`分支合并 `develop`分支上名的`folder/file.txt`文件

  ```
  git checkout master
  git checkout develop folder/file.txt
  git add.
  git commit -m 'desc'
  git push
  ```

## 撤销回退

- 未执行`git add`操作

  - `git clean -n`

    `-n`参数执行空执行，不执行真正清理操作

  - `git clean -f`

    清理新增文件

    清理新增目录，需加上`-d`参数

    删除`git`已忽略文件，需加上`-X`参数

  - `git checkout -- ./com/abc.txt`

    对已修改文件执行还原

    文件路径可通过`git status`获得

- 已执行`git add`操作

  - `git reset ./com/abd.txt`对文件状态执行还原；
  - `git checkout -- ./com/abc.txt`然后再对文件内容执行还原；
  - 注意：`git checkout`为不可恢复的替换操作，有需要可以用`git stash`替代;

- 已执行`git commit`操作

  - `git reset --soft HEAD~1`

    基于最近一次提交，多次执行会导致回到更早的版本，慎重！

- 还原合并请求

  合并代码后远程仓库会出现`Revert`按钮，可点击生成一个反向提交，实现还原

- 撤销提交请求

  与还原合并类似

- `reset`三选项

  - `git reset --hard`
    - 常用选项，较危险的操作
    - 将重置的 Staging Index（即 add 后的文件处于的树上，一般称为缓存区，以下用缓存区称呼）和 Working Directory（即 add 前的文件处于的树上，一般成为工作目录），缓存区和工作目录中被挂起的修改都将被重置
    - `reset --hard`后，可通过`reflog`重新找回
    - 彻底回退到某个版本，撤销`commit`并将修改后的文件重置
    - 注意：若文件未执行`commit`，`git reset --hard`操作是无法挽回的！
  - `git reset --mixed`
    - 默认模式
    - 缓存区被重置为指定提交
    - 缓存区中撤销的修改都被移到工作目录
  - `git reset --soft`
    - 缓存区和工作目录都保持不变
    - 撤销`commit`，不改变修改后的代码
    - 回退某个版本，只回退`commit`信息

## 常用命令汇总

本地仓库操作

| 命令              | 描述                     | 示例                   |
| ----------------- | ------------------------ | ---------------------- |
| git init          | 初始化仓库               | git init               |
| git add           | 添加文件/目录至暂存区    | git add file.txt       |
| git commit -m xxx | 暂存区文件提交至本地仓库 | git commit -m 描述信息 |

远程仓库操作

| 命令                        | 描述                           | 示例                                         |
| --------------------------- | ------------------------------ | -------------------------------------------- |
| git remote add xxx xxx      | 添加远程仓库                   | git remote add [仓库名] [仓库地址]           |
| git remote remove xxx       | 移除远程仓库                   | git remote remove [仓库名]                   |
| git pull xxx xxx            | 拉取分支代码                   | git pull [仓库名] [分支名]                   |
| git push xxx xxx            | 推送分支代码                   | git push [仓库名] [分支名]                   |
| git checkout -b xxx xxx/xxx | 新建本地分支并关联远程分支     | git checkout -b newLocalBranch origin/master |
| git branch -u xxx/xxx       | 更换当前本地分支关联的远程分支 | git branch -u origin/master                  |

## 分支命名约定

- master

  主分支，一般由 develop 和 hotfix 分支合并，任何时候都不能直接修改代码

- develop

  开发分支，一般开发新功能时，feature 分支都是基于 develop 分支下创建

- feature

  新功能开发分支，一般命名，feature/新功能「feature/login」

- release

  - 预上线分支，发布提测阶段；
  - 一组 feature 开发完成，代码全部合并至 develop 分支，进入提测，会创建 release 分支；
  - 当测试过程中存在 bug 需修复，开发者直接在该 release 分支修复提交；
  - 测试完成后，合并 release 分支到 master 与 develop 分支，用 master 代码上线；

- hotfix

  - 命名：hotfix/问题
  - 线上出现紧急问题，需及时修复，以 master 分支为基础，创建 hotfix 分支，修复完后，合并至 master 与 develop 分支；

## 提交日志规范

[参考](https://www.jianshu.com/p/b6ebc19b04f6)



