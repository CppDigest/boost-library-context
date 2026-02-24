# #527 This removes test project ID that will conflict with modular ID. [Merged]

> Username: grafikrobot  
> Created at: 2024-03-02 04:10:18 UTC  
> Updated at: 2024-03-04 15:55:56 UTC  
> Merged at: 2024-03-04 15:11:34 UTC  
> Closed at: 2024-03-04 15:11:34 UTC  
> Url: https://github.com/boostorg/hana/pull/527  

This removes the superfluous test project ID that will conflict with the modular project IDs for all libraries. When libraries are modular they will have the standard global ID of /boost/*lib-name*. Having colliding IDs will cause build errors.  
  
#modular-boost https://github.com/users/grafikrobot/projects/1

---

## Comment 1

> Username: ldionne  
> Created_at: 2024-03-04 14:45:04 UTC  
> Url: https://github.com/boostorg/hana/pull/527#issuecomment-1976744971  

I am not sure whether you want this merged right away or whether you need to wait before you do it -- please merge at your discretion. Thanks!

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-03-04 14:56:54 UTC  
> Url: https://github.com/boostorg/hana/pull/527#issuecomment-1976771617  

@ldionne I don't have permission to merge. And, yes, it would be great if it gets merged as soon as possible.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-03-04 15:23:04 UTC  
> Url: https://github.com/boostorg/hana/pull/527#issuecomment-1976832611  

PS. I just noticed I created the PR for master. Can you also apply it to the develop branch?

---

## Comment 4

> Username: ldionne  
> Created_at: 2024-03-04 15:24:35 UTC  
> Url: https://github.com/boostorg/hana/pull/527#issuecomment-1976835920  

Everything happens on `master` nowadays, `develop` isn't used and has gotten behind.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-03-04 15:54:06 UTC  
> Url: https://github.com/boostorg/hana/pull/527#issuecomment-1976901802  

Well that's not supposed to happen, develop should at least be kept in sync, even if unused.

---

## Comment 6

> Username: ldionne  
> Created_at: 2024-03-04 15:55:55 UTC  
> Url: https://github.com/boostorg/hana/pull/527#issuecomment-1976905910  

I just synced the branches.

---
