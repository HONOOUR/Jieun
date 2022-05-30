---
title: "Branching Strategy with Git"
date: "2022-05-30T22:20:10.284Z"
template: "post"
draft: false
slug: "git-branching"
category: "Development"
tags:
  - "Git"
  - "Branching"
description: "브랜치 전략, 릴리즈 관리 (master, dev, feature branch)"
socialImage: ""
---

### Central repo (two main branches)

- **origin/master** : production-ready state
- **origin/dev** : reflect the latest delivered development changes for the next release
    
    merged back into **master** and tagged with a release number at a stable point (integration branch)
    
    1. initialize(create) master 
    
    ![1](/media/git-1.jpg)
    
    ```bash
    git init 
    ```
    
    1. create and checkout dev branch from master
    
    ![2](/media/git-2.jpg)
    
    ```bash
    git checkout -b dev master
    ```
    
    1. merge branch dev into master
    
    ![3](/media/git-3.jpg)

    
    ```bash
    git checkout master
    git merge --no-ff dev
    ```
    
    ![4](/media/git-4.jpg)
    

### Feature branch

May branch off from: `develop`
Must merge back into: `develop`

- **non fast-forward(left)** : merge commit 이 생성됨 (—no-ff 옵션 필수)

```bash
git merge --no-ff feature
```

- **fast-forward(right)** : 다른 커밋한 내용과 구분하기 힘듬

![5](/media/git-5.jpg)

local branch 로 유지 후 삭제 또는 server 로 push 후 삭제 

```bash
$ git checkout dev              ## Switched to branch 'develop'
$ git merge --no-ff feature     ## Updating ea1b82a..05e9557
																## (Summary of changes)
$ git branch -d feature         ## Deleted branch myfeature (was 05e9557).
$ git push origin dev
```

1. create feature branch from dev

![6](/media/git-6.jpg)

![7](/media/git-7.jpg)

1. merge branch feature/web1 into dev

```bash
git checkout dev
git merge --no-ff feature/web1
```

![8](/media/git-8.jpg)

1. create feature 2 branch from dev
2. merge branch feature/web2 into dev

![9](/media/git-9.jpg)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6849951a-2496-4c24-91f8-0fe3e911b9f8/Untitled.png)

## Reference

[#dogfeet - A successful git branching model](https://dogfeet.github.io/articles/2011/a-successful-git-branching-model.html)

[A successful Git branching model » nvie.com](https://nvie.com/posts/a-successful-git-branching-model/)

[Git - Book (git-scm.com)](https://git-scm.com/book/ko/v2)