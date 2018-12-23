@[TOC](查看版本和配置用户信息)

### 查看版本 or 确认是否安装成功

```
$ git --version
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2018122220243794.jpg)

### 配置 User 信息

```
// 配置 user.name
$ git config [--local | --global | --system] user.name '你的名字'
// 配置 user.email
$ git config [--local | --global | --system] user.email '你的邮箱地址'
// 显示 config 的配置
$ git config --list [--local | --global | --system]
// 清除 config 配置
$ git config --unset [--local | --global | --system] user.name
```
> config 的三个作用域(优先级: local > global > system)
> `--local` (缺省, 并且只能够用在 git 仓库中): 只对某个仓库有效;
> `--global`: 对当前用户所有仓库有效, 系统默认在此作用域配置了 git 的帮助文件的路径;
> `--system`: 对系统所有登陆的用户有效;

---
