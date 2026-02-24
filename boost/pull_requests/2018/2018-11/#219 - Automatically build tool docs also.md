# #219 Automatically build tool docs also. [Closed]

> Username: grafikrobot  
> Created at: 2018-11-19 19:19:42 UTC  
> Updated at: 2018-11-30 07:26:57 UTC  
> Closed at: 2018-11-29 20:52:54 UTC  
> Url: https://github.com/boostorg/boost/pull/219  

Corresponding change to https://github.com/boostorg/boost/commit/6bc65435dc18e5d4c64cd43cac6779a903cc3edd

---

## Comment 1

> Username: mclow  
> Created_at: 2018-11-20 23:41:19 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-440468775  

This looks fine to me.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2018-11-21 17:42:53 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-440753023  

Someone with the right perms should merge this. @mclow ? @pdimov ?

---

## Comment 3

> Username: mclow  
> Created_at: 2018-11-28 05:51:18 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442328607  

Whoops. This should go into `develop`, right?

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2018-11-28 06:22:02 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442333787  

The change has been in develop for some time by now. This is the corresponding change for master, i.e. for the release.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-11-28 23:42:48 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442649290  

Shouldn't we always be merging develop into master, not picking things?

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2018-11-29 19:22:17 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442960061  

@jeking3   
  
1. Historically we have not done merges on the super project from develop to master because of how the submodules work.  
2. If you think that's a problem it should be brought up in the dev list.  
  
For others: without this commit tools without BoostBook integrated documentation will not have documentation included in the release.

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-11-29 19:29:13 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442962388  

> This branch is 23014 commits ahead, 8926 commits behind master.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2018-11-29 19:49:39 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442969386  

23015 commits ahead now ;-) They keep coming, and coming.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-11-29 19:59:52 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442972799  

https://github.com/boostorg/boost/commit/36b81c46be50588e599a498c4d2261da3b0f8ab2

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-11-29 20:36:31 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442984056  

So this can be closed?

---

## Comment 11

> Username: grafikrobot  
> Created_at: 2018-11-29 20:52:54 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-442988986  

I guess it can be closed.. @pdimov and I must have crossed paths.

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-11-30 00:15:21 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-443042657  

I cherry-picked the develop commit before realizing I could just have merged the PR.

---

## Comment 13

> Username: Mike-Devel  
> Created_at: 2018-11-30 07:26:57 UTC  
> Url: https://github.com/boostorg/boost/pull/219#issuecomment-443115845  

@grafikbot:  
  
> Historically we have not done merges on the super project from develop to master because of how the submodules work.  
  
Could you elaborate a bit on that (just curious)

---
