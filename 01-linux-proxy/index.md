# Linux 及 Git 代理配置


Linux 终端代理及 Git Http & SSH 代理最佳实践

<!--more-->

![Linux-shell](https://linuxstory.org/wp-content/uploads/2018/08/xl-2017-linux-terminal-1.jpg)



## Linux 终端代理配置

将如下配置添加至 ~/.bash_profile 后, source ~/.bash_profile 生效.

```bash
http_proxy=http://127.0.0.1:7890
https_proxy=http://127.0.0.1:7890
no_proxy=localhost,127.0.0.1,localaddress,.localdomain.com,192.168.0.0/16,10.0.0.0/8,172.16.0.0/12,100.64.0.0/10,17.0.0.0/8,.local,169.254.0.0/16,224.0.0.0/4,240.0.0.0/4

# 如果代理失效的话直接运行 poff 即可断开 proxy
alias poff='unset http_proxy;unset https_proxy'
# 快捷方式打开
alias pon='export http_proxy=$proxyurl; export https_proxy=$proxyurl'
```

## Git 代理配置

### Git HTTP 代理

适用于仅 pull  场景

1. 临时

```bash
git clone -c http.proxy="http://127.0.0.1:1080" https://github.com/TIGERB/easy-php.git
```

1. 永久

```bash
# 设置 git 的代理相关设置
git config –global http.proxy http://127.0.0.1:1080
git config –global https.proxy http://127.0.0.1:1080
# 或者使用 sock5
git config --global http.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080
# 关闭SSL认证
git config --global http.sslVerify false
```



```bash
# 取消 git 的代理相关设置
git config –global –unset http.proxy
# 取消 git 的代理相关设置
git config –global –unset https.proxy
```

```bash

# 查看 Git 全局配置
git config --global -l

# 取消代理设置
git config --global --unset http.proxy
```

## Git SSH 代理

适用于 pull/post 场景

```bash
# vim ~/.ssh/config
Host github.com bitbucket.org
    ProxyCommand            nc -x 127.0.0.1:1080 %h %p
```


### 通过 https 端口使用 SSH

由于部分代理限制了 22 端口，无法直接使用 ssh 模式 问题详见 [git无法使用代理连接ssh](https://github.com/yichengchen/clashX/discussions/942)

可将如下配置添加至 ~/.ssh/config 中

```bash
Host github.com
  Hostname ssh.github.com
  Port 443
  User git
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa
```

## 参考

- [https://docs.github.com/en/authentication/troubleshooting-ssh/using-ssh-over-the-https-port](https://docs.github.com/en/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)
