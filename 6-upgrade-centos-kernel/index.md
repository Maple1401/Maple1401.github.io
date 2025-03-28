# centos 内核升级


在线及离线升级 centos7 内核方法
<!--more-->

## 在线升级流程

1. 检查当前的内核版本:


```shell
uname -r
```

2. 安装 ELRepo 仓库,这是一个提供更新内核的第三方仓库,就可以直接使用 yum 更新内核:

```shell
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
```

3. 列出可用的内核版本包:

```shell
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
```

这可以看到 ELRepo 中提供了哪些更新的内核版本包。

1. 选择一个需要升级的内核版本并安装（版本解释往下拉）:

```shell
yum --enablerepo=elrepo-kernel install kernel-lt
```

将 lt 替换为你选择的内核版本。这将安装新的内核版本。

1. 设置 grub 默认使用新的内核版本启动:

```shell
grub2-set-default 0

# 0 是新内核在 grub 菜单中的序号,可以通下面命令查看序号
awk -F\' '$1=="menuentry " {print i++ " : " $2}' /boot/grub2/grub.cfg
```


1.  重启系统:

```shell
# 重启生效
reboot
```


内核版本解析

kernel-lt 在 ELRepo 仓库中的内核包命名中,是“Long Term”的缩写,代表长期支持的内核。
ELRepo 中提供多种内核包,包括:
- kernel-lt - 长期支持内核
- kernel-ml - 主线(主开发分支)内核
- kernel-nl - 非长期支持的最新内核

```shell
# 列出可用的内核包
yum --enablerepo=elrepo-kernel list available
kernel-lt.x86_64                  5.4.255-1.el7.elrepo
kernel-ml.x86_64                  6.1.6-1.el7.elrepo
kernel-ml-devel.x86_64            6.1.6-1.el7.elrepo
kernel-nl.x86_64                  6.1.6-1.el7.elrepo

```
- kernel-lt 对应版本是 5.4.255
- kernel-ml 对应版本是 6.1.6
- kernel-nl 对应版本也是 6.1.6

## 为什么有的教程让修改 grub 文件

在 CentOS 7 中配置 GRUB 默认内核的方法有两种:

1. 修改 /etc/default/grub 文件中的 GRUB_DEFAULT 参数
2. 使用 grub2-set-default 命令

对于第一种方法,修改 /etc/default/grub,需要在改完参数后运行: `grub2-mkconfig -o /boot/grub2/grub.cfg``

这是因为修改了 /etc/default/grub 只是改变了配置文件,还需要用 grub2-mkconfig 来根据配置文件生成实际的引导菜单配置文件 `/boot/grub2/grub.cfg`

所以一些教程会建议完整的步骤是:
```shell
vim /etc/default/grub
# 修改 GRUB_DEFAULT=0
grub2-mkconfig -o /boot/grub2/grub.cfg
```


而第二种方法使用 grub2-set-default 命令,它会直接修改 /boot/grub2/grub.cfg 文件,所以不需要 grub2-mkconfig 步骤。

这两种方法最终效果都是修改了 grub.cfg 文件中默认的内核入口,以实现设置默认内核的目的。

但 grub2-set-default 方法更简单直接,不需要手动修改配置并生成 grub.cfg 文件,所以更推荐使用这种方法,可以避免一些手动步骤带来的问题。
总结一下:

- 修改 /etc/default/grub 需要额外 grub2-mkconfig 生成 grub.cfg
- grub2-set-default 直接修改 grub.cfg,更简单直接

所以如果可以的话,建议使用 grub2-set-default 的方式来设置 GRUB 默认内核，更简单。


## 如何离线安装

1. 如果你想自己获取，在完成上面的 repo以及列出可用内核后，可以指定下载离线 rpm 安装包
yum --disablerepo="*" --enablerepo="elrepo-kernel" --downloadonly --downloaddir=/path/to/store/rpms install kernel-lt
