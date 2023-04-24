---
layout: post
title: "My git aliases"
date: 2023-04-18 07:00:00 -0000
categories: git
---

When I work on some code development, I like to use git. It helps keep track of my work, organize history.
But I don't like writting too much in the console. So I'm using some aliases to help me:

|Alias|Meaning|
|-------:|:-------|
|`git st`|`git status`|
|`git cm "<message>"`|`git commit -m "<message>"`|
|`git co <name>`|`git checkout <name>`|
|`git cob <name>`|`git checkout -b <name>`|
|`git pl`|`git pull`|
|`git ph`|`git push`|
|`git phf`|`git push --force`|
|`git lg`|`git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --`|

You can add your own aliases, like so:
```
git config --global alias.phf "push --force"
```

I especially like the `log` alias, which produces comprehensive output:

![Sample git lg output](/assets/images/sample_git_lg_output.png)