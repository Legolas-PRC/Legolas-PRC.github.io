# 上线时遇到的问题

## pull request

> A pull request is a proposal to merge a set of changes from one branch into another.  

PR是将一组更改从一个分支**合并**到另一个分支的提议,本质上就是merge

当源分支和目标分支都修改了同一个文件的同一个地方，会导致冲突（merge-conflicts），需要手动解决冲突：  

1. 本地拉取最新的源分支和目标分支
2. 切换至源分支，将目标分支合并至源分支，手动解决冲突
3. 提交新的commit，并push到远程

::: tip 合并目标分支代码后再提交，其他人的部分需要跑覆盖率吗？
不需要，因为上线覆盖率是按照源分支和目标分支diff部分的代码算的
:::

https://www.atlassian.com/git/glossary#commands

https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests

## Jar包发版

开发时用SNAPSHOT快照版本，如果有人也发版同一个jar包，应该用不同版本号  
上灰度前发布稳定版本，修改代码版本号；如果有多个人同时使用，后上的会因为冲突而需要先将灰度分支合并到开发分支里，合并后再发新的稳定版就包含了前面人的内容
