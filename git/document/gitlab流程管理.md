基于GitLab版本管理以及代码审查:kissing_cat:

---



[TOC]

# 基于GitLab版本管理和代码审查

## 1.工作区域

git的工作区域分成本地客户端和和远程服务端。

![git工作区域图](https://github.com/paningking/notes/blob/master/git/png/git%E5%B7%A5%E4%BD%9C%E5%8C%BA%E5%9F%9F%E5%9B%BE.png?raw=true)

Working Directory：工作目录，即正在编辑的文件状态。文件状态为未跟踪（untrack）和已修改（modified）在此区域内。
Staging Area：暂存区，保存了下次将提交的文件列表信息。文件状态为已暂存（staged）在此区域内。
Local Repository：本地仓库，执行commit命令后文件将提交到本地仓库。
![流程图2](https://github.com/paningking/notes/blob/master/git/png/git%E6%B5%81%E7%A8%8B%E5%9B%BE2.png?raw=true)
Remote Repository（远程仓库）：已提交到远程git服务端的文件。

## 2.常用命令
git clone：将远程仓库clone到本地计算机。
git pull ：拉取远程仓库的数据。
git status ：展示工作区及暂存区域中不同状态的文件。
git add：将内容从工作目录添加到暂存区。
git commit：所有通过 git add 暂存的文件提交到本地仓库。
git push ：将本地仓库的记录提交到远程仓库。
git reset HEAD <file> ：从暂存区移除指定文件。
git checkout -- <file> ：从本地仓库恢复指定文件。
git checkout branch：检出分支。
git merge --no-ff branch：将指定分支合并到检出分支上。

## 3.git文件状态
目录下的每一个文件都不外乎这两种状态：已跟踪或未跟踪。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。 工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

![git文件状态图.png](https://github.com/paningking/notes/blob/master/git/png/git%E6%96%87%E4%BB%B6%E7%8A%B6%E6%80%81%E5%9B%BE.png?raw=true)

git库所在的文件夹中的文件大致有4种状态：

- **Untracked**: 未跟踪，此文件在文件夹中，但并没有加入到git库, 不参与版本控制。通过`git add` 状态变为`Staged`。

- **Unmodify**: 未修改，即版本库中的文件快照内容与文件夹中完全一致。 这种类型的文件有两种去处：如果它被修改，而变为`Modified`；或者使用`git rm`移出版本库，则成为`Untracked`文件。

- **Modified**: 已修改，仅是修改并没有进行其他的操作。这个文件也有两个去处：通过`git add`可进入暂存`staged`状态；或者使用`git checkout` 从库中取出文件，覆盖当前修改，返回到`unmodify`状态。

  ![git_status_modified1.png](https://github.com/paningking/notes/blob/master/git/png/git_status_modified1.png?raw=true)

  ![git_status_modified2.png](https://github.com/paningking/notes/blob/master/git/png/git_status_modified2.png?raw=true)

  ![git_status_modified3.png](https://github.com/paningking/notes/blob/master/git/png/git_status_modified3.png?raw=true)

- **Staged**: 暂存状态。执行`git commit`则将修改同步到库中，这时库中的文件和本地文件又变为一致，文件为`Unmodify`状态。执行`git reset HEAD filename`取消暂存，文件状态为`Modified`。
  ![git_status_staged.png](https://github.com/paningking/notes/blob/master/git/png/git_status_staged.png?raw=true)

## 4.标准GitFlow工作流

### 4.1标准工作流

一般情况下的gitfow工作流包括：master（主分支），release（预发布分支），develop（开发分支），feature（功能分支），fixbug（修复分支），hotfix（紧急修复分支）等。如下图所示：

![git分支流程图.jpg](https://github.com/paningking/notes/blob/master/git/png/git%E5%88%86%E6%94%AF%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg?raw=true)

- master分支，即主分支。任何项目都必须有个这个分支。对项目进行tag或发布版本等操作，都必须在该分支上进行。分支一般由develop以及hotfix分支合并，任何时间都不能在master分支直接修改代码。
- develop分支，即开发分支，从master分支上检出。始终保持最新完成以及bug修复后的代码，团队成员一般不会直接更改该分支，而是分别从该分支检出自己的feature分支，开发完成后将feature分支上的改动merge回develop分支。
- release分支，即预发布分支，从develop分支上检出，正式版本发布之前（合并到master分支之前），需要有一个预发布的版本进行测试。可进行简单的bug修复，如果bug修复比较复杂，可merge回develop分支后由其他分支进行bug修复。此分支测试完成后，需要同时merge到master和develop分支上。
- feature分支，即功能分支，从develop分支上检出。团队成员中每个人都维护一个自己的feature分支，并进行开发工作，开发完成后将此分支merge回develop分支。此分支一般用来开发新功能或进行项目维护等。
- fixbug分支，即修复分支，从develop分支检出，用作bug修复，bug修复完成需merge回develop分支，并将其删除。
- hotfix分支，即紧急修复分支。和fixbug分支的区别在于，该分支由master分支检出，进行线上版本的bug修复，修复完成后merge回master分支，由master合并到develop分支上，merge完成后也可以将其删除。

### 4.2分支命名约定

| 分支名称             | 名称格式             | 说明                                                        |
| ----------------- | ----------------| ------------------------------------ |
| master| master  | 有且只有一个，用于部署生产环境的分支。                 |
| release| release-`*` | `*`可以是版本发布日期、版本号、需求名称缩写、issuse编号，也可只用一个release分支。 |
| develop| develop-`*`  | `*`可以是版本发布日期、版本号、需求名称缩写、issuse编号，也可只用一个develop分支。 |
| feature| feature-{username}-`*` | 开个人员个人分支，`*`通常是版本发布日期、版本号、需求名称缩写、issuse编号，也可只用feature-{username}分支。 |
| fixbug | fixbug-`*` | `*`通常是版本发布日期、版本号、需求名称缩写、issuse编号。 |
| hotfix| hotfix-`*` | `*`通常是版本发布日期、版本号、需求名称缩写、issuse编号。 |

## 5.本项目GitFlow工作流

![master-develop-feature-release-kbrb.png](https://github.com/paningking/notes/blob/master/git/png/master-develop-feature-release-kbrb.png?raw=true)

1.**创建主分支master**
   主分支master分支是仓库默认分支，仓库owner创建好仓库后，master角色push正确的代码到master分支。有且只有一个master分支。

2.**创建开发分支develop**
   由master角色从master主分支创建。有且至少有一个develop分支。

```
# 创建Develop分支
git checkout -b develop master
```

3.**创建功能分支feature**
   每个开发人员从develop分支创建自己的feature分支进行开发。功能分支的名字，采用feature-{username}的形式命名。
```
# 创建一个功能分支：
git checkout -b feature-my develop
```

4.**合并功能分支feature**
   开发完成一个功能点后，开发人员将feature分支合并到的develop分支之前，需要先从develop分支合并到自己的feature分支，如果有冲突，需要解决冲突后才能合并。（--no-ff`：保留分支的commit历史。）
```
# 开发完成后，将功能分支合并到develop分支
git checkout develop
git merge --no-ff feature-my
```

5.**删除功能分支feature**
   feature分支合并到对应的develop分支之后，发布到测试环境进行测试，测试没问题后可删除feature分支。测试中发现问题，可以在feature分支就行修复，跳转到上一步骤。
```
# 删除feature分支：
git branch -d feature-my
```

6.**合并开发分支develop**
   develop分支在测试环境测试通过之后，合并到release分支并发布到预发布环境进行测试。
```
# 创建一个预发布分支：
git checkout -b release-1.2.0 develop
```

7.**合并预发布分支release**
   release分支在预发布环境验证通过后，release是从develop分支上面分出来的，预发布结束以后，必须合并进develop和master分支。在预发布环境测试出bug，如果bug**非常简单**，可以直接在release分支修复，然后继续提测。否则，将release分支merge回develop分支后由fixbug分支进行修复。

```
# 确认没有问题后，合并到develop分支：
git checkout develop
git merge --no-ff release-1.2.0
# 确认没有问题后，合并到master分支：
git checkout master
git merge --no-ff release-1.2.0
# 对合并生成的新节点，做一个标签
git tag -a 1.2.0 -m '部署包版本名' 
```

8.**修复分支fixbug**
   修复分支和feature本质上是一样的，只是功能不一样，流程和操作参照feature分支。

9.**合并 master分支**
   master分支发布到生产环后，master分支如果发生变更，需要从master分支合并到develop分支，可以考虑定期合并一次。
```
git checkout develop
git merge --no-ff release-1.2.0
```
  再合并到develop分支：
```
git checkout develop
git merge --no-ff release-1.2.0
```
  最后，删除预发布分支：
```
git branch -d release-1.2.0
```
## 6.GitLab Code Review 配置

### 6.1新建issue

每次开发新需求、修复bug或者做优化都可以在gitlab新建issue，这样来最终整个过程。

![list_issue.png](https://github.com/paningking/notes/blob/master/git/png/list_issue.png?raw=true)

![creat_issue.png](https://github.com/paningking/notes/blob/master/git/png/creat_issue.png?raw=true)

### 6.2环节选定
以上述Git工作流为例，开发人员在feature分支进行开发，开发完成后merge到develop分支进行测试。那么最适合做Code Review就是feature分支合并到develop分的环节。
![master-develop-feature-release-codereview-kbrb.png](https://github.com/paningking/notes/blob/master/git/png/master-develop-feature-release-codereview-kbrb.png?raw=true)

### 6.3.GitLab Repository 配置

为了保证必须以Merge的方式变更develop分支、release分支、以及master分支，我们需要对Push以及Merge权限进行限制，实行分支保护。

菜单：Settings->Repository Settings然后展开Protected Branches选项

![protected_branch.png](https://github.com/paningking/notes/blob/master/git/png/protected_branch.png?raw=true)

一般情况下，所有的开发人员对develop分支、release分支、以及master分支均无push权限，只能以merge方式合并到对应分支，而且只有Masters组的用户有Merge权限。对于通配符的分支保护，我们需要给Masters组的用户Push权限，不然无法新建对应格式的分支。对于固定名称的分支：develop、master、release，可以限制所有角色都没有push权限。

同时为了兼顾开发便利性和代码审查质量，我这边设置了双重代码审查，所以放开了Developers组对develop分支的merge权限，但是禁止push。然后我会在develop分支合并到release分支进行预发布测试的之前再次进行代码审查。

## 7.GitLab Code Review 示例

### 7.1新建Merge Request

在左侧仓库属性中找到Merger Request链接或者右上角用户模块找到，进入Merge Request列表，点击绿色的New merger request按钮就可以新建Merge Quest。

![merge-request-list.png](https://github.com/paningking/notes/blob/master/git/png/merge-request-list.png?raw=true)

![new_merge_request.png](https://github.com/paningking/notes/blob/master/git/png/new_merge_request.png?raw=true)

| 填写项             | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| Title              | 标题，没有特殊要求保持默认即可                               |
| Description        | 描述，需要将变更的需求描述清楚，最好附件Code Review要点      |
| Assignee           | 分配到的人，被分配到的人将会收到邮件通知，跟Merge权限没有必然关系，仍然是项目的Maintainers（Masters）角色拥有Merge权限 |
| Milestone          | 里程碑，如果没有可不选                                       |
| Label              | 标签，如果没有可不选                                         |
| Approvers user     | 批准人/审批人，必须为项目所在组成员，如果选择了批准人，那此次合并必须经由批准人批准 |
| Approvers group    | 批准人组，方便同时选择多个批准人                             |
| Approvals required | 最少批准个数，如果选了个3个批准人，Approvals required设置为1，那么只需要1个批准人批准即可 |
| Source branch      | 源分支，跟上一步骤选择一致，这里主要用于确认                 |
| Target branch      | 目标分支，跟上一步骤选择一致，这里主要用于确认               |

Merge Request 创建之后就会转到该页面，被分配到的人（Assignee）会收到邮件提醒，如果需要多个人进行 Code Review，只要将该页面的链接发给其他项目成员即可。项目成员可以查看变更并评论，只不过按照之前的配置，只有Masters角色的成员才有Merge的权限。

### 7.2code review

当我们收到开发人员提交的merge request的时候，可以在左侧仓库中找到Merger Request链接或者右上角用户模块找到。点击链接就可以看到所有需要自己处理的merge request。

![merge-request-list.png](https://github.com/paningking/notes/blob/master/git/png/merge-request-list.png?raw=true)

点击进去查看详情，包括标题，讨论记录、提交信息，修改记录等。在Changes选项卡中，我们可以看到所有的变更，可以查看并编辑待合并的文件，可以直接打开审查代码，也可以在gitlab自己的web ide里面打开审查代码。将光标移动到Discussion处会出现评论按钮，我们可以点击评论按钮发起评论，这个评论是对项目成员可见的，大家可在讨论区进行讨论。最终讨论发起者有权将讨论标记为已解决resolved。

![merge_request_process](https://github.com/paningking/notes/blob/master/git/png/merge_request_process.png?raw=true)

如果对代码或者其他不满意，在评论区域写清楚拒绝合并的原因和指导建议等，点击Comment提交自己的评论，或者点击Comment and close merger request评论并关闭。

![merge-process.png](https://github.com/paningking/notes/blob/master/git/png/merge-process.png?raw=true)

### 7.3合并Merger Request

当所有的问题已解决之后（如果选择了审批人也需要审批通过），Masters成员点击Merge完成合并即可，填写评论并点击绿色的Merge按钮，如果需要合并后删除源分支，勾选Remove source branch单选框。

![merger-request.png](https://github.com/paningking/notes/blob/master/git/png/merger-request.png?raw=true)

点击Merger按钮后的merger request状态就变成已关闭。

![merged.png](https://github.com/paningking/notes/blob/master/git/png/merged.png?raw=true)

## 8.JetBrains IDE GitLab插件使用

前面介绍了通过GitLab网页创建Merge Request并发起Code Review，但作为开发人员，还是结合IDE来使用会更顺手，GitLab提供了相关的api，只要我们创建响应的token，就可以供IDE插件来访问GitLab，以便使用IDE代替在网页上操作。

### 8.1GitLab Access Token

菜单：User Settings->
Access Tokens 进入Access Token添加页面

![access-token](https://github.com/paningking/notes/blob/master/git/png/access-token.png?raw=true)

| 项         | 说明                                                 |
| ---------- | ---------------------------------------------------- |
| Name       | 名称，根据自己喜好来即可                             |
| Expires at | 过期时间，最远可以选择到10年后，根据自己需要填写即可 |
| Scopes     | 范围，这里选择api就够用了                            |

创建完成后，暂时保存token。因为一旦刷新或者重开页面，token就不可见了。

![access-token2](https://github.com/paningking/notes/blob/master/git/png/access-token2.png?raw=true)

### 8.2JetBrains IDE GitLab插件使用

安装以下两个插件即可：Gitlab Projects，Gitlab Integration。
- 安装GitLab插件
Settings->Plugins 进入Plugins管理页

![idea-gitlab-plugin1](https://github.com/paningking/notes/blob/master/git/png/idea-gitlab-plugin1.png?raw=true)

安装完成重启IDE生效。

- 配置GitLab

在Settings->Tools->GitLab Integration配置。

填写GitLab Server Url、Access Token，然后点击`Add New One`完成添加。如果是私有化部署的GitLab，换成对面的域名或者IP+Port即可。

![idea-gitlab-integration](https://github.com/paningking/notes/blob/master/git/png/idea-gitlab-integration.png?raw=true)

配置正常后，可以在工程目录区域选择任意文件，右键打开就可以看到Gitlab菜单下的Create Merge Request和List Merge Requests。

![idea-gitlab](https://github.com/paningking/notes/blob/master/git/png/idea-gitlab.png?raw=true)

- Create Merge Request

Clone项目feature-wpn分支到本地，变更后push到origin。

![idea-create-merge](https://github.com/paningking/notes/blob/master/git/png/idea-create-merge.png?raw=true)

然后在主菜单VCS或者右键文件菜单选择：Git->Git Lab-> Create Merge Request。

![idea-create-merge](https://github.com/paningking/notes/blob/master/git/png/idea-create-merge.png?raw=true)

这里相当于我们在GitLab网页上进行创建操作，只不过少了一些选项，也暂不支持Approvers相关选项。选择目标分支，被分配的人，填写好Title、Description然后点击OK即可。Merge Request创建完成后，插件会在右下角提示，点击链接即可跳转到Merge Request页面。

> 如果提示冲突，请先将目标分支代码合并到当前分支

- Merge Request Manage

项目成员在主菜单VCS或者右键文件菜单选择：Git->Git Lab-> List Merge Request。在这里可以看到待处理的Merge Request，选中后点击`Code Review`就可以呼出Merge Request操作面板。

| 按钮         | 说明                   |
| ------------ | ---------------------- |
| Diff         | 查看所有变更文件及差异 |
| Comments     | 查看、添加评论         |
| Assign to me | 将跟进人指给自己       |
| Merge        | 执行Merge              |

![idea-create-merge2](https://github.com/paningking/notes/blob/master/git/png/idea-create-merge2.png?raw=true)

- Merge Request Diff

Diff界面说明：

![idea-create-merge3](https://github.com/paningking/notes/blob/master/git/png/idea-create-merge3.png?raw=true)

![idea-create-merge4](https://github.com/paningking/notes/blob/master/git/png/idea-create-merge4.png?raw=true)

- Merge Request Comments

Comments界面可以查看指定Merge Reuqest评论信息，也可以添加评论，双击可以查看完整评论内容。但是不支持针对代码行发起讨论、对讨论标记为已解决等。

![idea-merge-comment](https://github.com/paningking/notes/blob/master/git/png/idea-merge-comment.png?raw=true)
