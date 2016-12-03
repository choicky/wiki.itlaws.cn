# 普通用户命令补全

在~/.bashrc 文件中增加：
```
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
```

然后 `source ~./bashrc`加载新设置

# 普通用户加 `sudo` 命令补全

在~/.bashrc 文件中增加：
    complete -cf sudo

然后 `source ~./bashrc`加载新设置

# Ubuntu 安装与配置

 * [Ubuntu 16.04 设置sshd并启用ufw和swapfile](Ubuntu-16.04-sshd-ufw-swapfile)