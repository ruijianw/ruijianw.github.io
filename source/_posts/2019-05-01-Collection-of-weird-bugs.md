---
title: Collection of weird bugs
mathjax: false
date: 2019-05-01
updated: 2019-10-21
tags:
---

# Jupyterlab loads too slow
## Description
I setup my jupyter server on an EC2 instance and open it with my laptop browser, but it is getting slower and slower when I try to reopen the window.

I looked at the Chrome's Dev tools, it turns out there is a `js` file 36MB size, that means each time you need to download 36MB js file to open a jupyter lab page.

## Solution
Build the jupyter lab with following command:

```bash
jupyter lab build --minimize=False --dev-build=False
```
 


# Git cannot see new files
### Description:
1. New files cannot be found under `git status`
2. There is a weird icon file, refer to [this link](https://superuser.com/questions/298785/icon-file-on-os-x-desktop)
3. It is not the `.gitignore` file issue, excluded by `git status --ignored`.
4. The cloned github folder was on **Google Drive** synchronization folder.

<!--more-->
### Solution:
Move the folder out of the Google Drive, everything works.

Although the root cause is not found, my guess would be that some indexing of Google Drive makes the git misbehaving.

---