---
title: git 拉取所有远程分支
date: 2018-07-09 01:22:30
tags: git
---
```
git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
git fetch --all
git pull --all
```

来自Stackoverflow

链接：http://stackoverflow.com/questions/10312521/how-to-fetch-all-git-branches
