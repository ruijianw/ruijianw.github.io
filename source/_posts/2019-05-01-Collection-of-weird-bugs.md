---
title: Collection of weird bugs
mathjax: false
date: 2019-05-01
updated: 2019-05-01
tags:
---

# Git cannot see new files
### Symptom:
1. New files cannot be found under `git status`
2. There is a weird icon file, refer to [this link](https://superuser.com/questions/298785/icon-file-on-os-x-desktop)
3. It is not the `.gitignore` file issue, excluded by `git status --ignored`.
4. The cloned github folder was on Google Drive.

### Solution:
Move the folder out of the Google Drive, everything works.

Although the root cause is not found, my guess would be that some indexing of Google Drive makes the git misbehaving.

---