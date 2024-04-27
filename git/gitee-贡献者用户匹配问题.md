# gitee贡献者用户匹配问题

gitee提交代码时，关于贡献者，会通过`git config`配置的`user.email`来匹配用户，无论某个账号的主邮箱还是提交邮箱匹配与提交时你设置的`user.email`一致，则认为贡献者就是这个被匹配到的用户，与你提交时具体登陆的账号没有关系。如果匹配不到，贡献者的名字会显示为会通过`git config`配置的`user.name`，邮箱会显示为通过`git config`配置的`user.email`，在gitee的网站中点击贡献者的头像会跳转发email。如果没有配置`user.email`,无论是否配置了`user.name`，贡献者的名称会设置为操作系统的账户的`全称`，email会设置为`账户名称@本机内网ip`（注意`账户名称`和账户`全称`不一样，这里使用mac做的实验，windows情况不详）。
