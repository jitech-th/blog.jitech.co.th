---
tags:
    - Error
    - Git
---
# Push rejected after stashing commits

```
$ git push
To gitlab.com:wararit.ho/homelab-blogs.git
 ! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to 'gitlab.com:wararit.ho/homelab-blogs.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Go to project: `Settings` -> `Repository` -> `Expand` on `Protected branches`

```
$ git push -f
Enumerating objects: 14, done.
Counting objects: 100% (14/14), done.
Delta compression using up to 4 threads
Compressing objects: 100% (8/8), done.
Writing objects: 100% (10/10), 2.48 KiB | 846.00 KiB/s, done.
Total 10 (delta 2), reused 0 (delta 0), pack-reused 0
To gitlab.com:wararit.ho/homelab-blogs.git
 + f38264c...ae1c24f main -> main (forced update)
```
