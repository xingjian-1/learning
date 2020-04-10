## Git
git clone git@gitlab.alipay-inc.com:common_release/test.git 下载代码
git branch –a 查看当前仓库的所有分支；git checkout dev 切换分支； git branch –r查看远程仓库的所有分支

## Git使用
例如：SIT分支是一个稳定版本的分支，所有的代码都要合并到这个分支。现在基于SIT分支分别创建Dev1、Dev2分支用于开发使用，2月份Dev1有一个需求迭代，合并到了SIT分支，3月份Dev2开发了一个基础组件通用功能，要合并到SIT分支，在合并到SIT分支之前想在Dev2分支上获取当前系统最新代码在本地跑一下测试一遍，Dev2测试完没问题，再推送到SIT分支上。
#### SIT分支代码合并到Dev2分支
![add image](/img/fetch.png)
![add image](/img/把别的分支合并到现在的分支.png)
![add image](/img/选择把哪个分支代码合并过来.png)
