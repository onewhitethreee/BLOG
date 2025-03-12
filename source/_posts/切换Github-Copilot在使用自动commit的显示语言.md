---
uuid: 29ad5800-ff99-11ef-89af-9b39bec12ed9
title: 切换Github Copilot在使用自动commit的显示语言
date: 2025-03-13 00:24:36
tags: [github]
---
因为默认的生成语言是中文，想要切换到英文。找了一大圈没有找到在哪里修改，不过最后还是找到了。发一个帖子来记录一下

vscode中输入*ctrl + shift + p*

```
GitHub.copilot.chat.localeOverride
```

把原来的auto改为en就是英文

![](https://img.164314.xyz/2025/03/280d189d7c09476d09d6e980501f7043.png)

这样子在使用自动commit的时候就是英文了

![](https://img.164314.xyz/2025/03/c3c46e25e4ef7f519563fdd215cdc19f.png)
