---
uuid: 212b6900-17c3-11f0-ba3e-7fd65c8aee6c
title: ZSH 技巧：无需输入 ./ 就能运行当前目录下的程序
date: 2025-04-12 19:25:29
tags: 
  - ZSH
  - 技巧
  - 命令行
  - Linux
categories: Linux

---

你是不是经常遇到在网上复制命令的时候粘贴到自己的机子上想要运行却提示command not found?

我也是，找了一大圈我终于找到了解决方案，只需要在zsh的配置文件中添加一个函数就好了

```
command_not_found_handler() {
  if [ -x "./$1" ]; then
    echo "执行本地程序: ./$1 ${@:2}"
    ./$1 "${@:2}"
    return 0
  else
    echo "zsh: command not found: $1"
    return 127
  fi
}
```

把这个粘贴到zsh配置文件中，然后source一下，再次运行就少掉了每次运行都要添加./的烦恼了。

效果如下

![](https://img.164314.xyz/2025/04/01062620df509298603ac725c49b0175.png)

---

哦对了，我是保证在我运行的目录下面是有这么一个二进制文件存在的