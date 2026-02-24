# #185 Avoid errors when tool submodules do not exist. [Merged]

> Username: grafikrobot  
> Created at: 2018-05-14 04:25:23 UTC  
> Updated at: 2018-05-14 16:06:04 UTC  
> Merged at: 2018-05-14 15:41:52 UTC  
> Closed at: 2018-05-14 15:41:52 UTC  
> Url: https://github.com/boostorg/boost/pull/185  



---

## Comment 1

> Username: grafikrobot  
> Created_at: 2018-05-14 04:30:36 UTC  
> Url: https://github.com/boostorg/boost/pull/185#issuecomment-388694189  

FYI.. This fixes the errors for anyone who is using the common CI scripts for individual library CI testing. For example Predef:  
https://travis-ci.org/boostorg/predef/builds/378550316  
https://ci.appveyor.com/project/grafikrobot/predef-ga22y

---

## Comment 2

> Username: mjcaisse  
> Created_at: 2018-05-14 05:38:26 UTC  
> Url: https://github.com/boostorg/boost/pull/185#issuecomment-388702401  

Hi @grafikrobot - The checks indicate failure. Is that expected?

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2018-05-14 08:57:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/185#pullrequestreview-119745104  

📁 status/Jamfile.v2

```diff
 200 |- local tools-to-test = bcp check_build quickbook ;
 200 |+ local tools-to-test = ;
 201 |+ bcp check_build quickbook ;
```

> Username: pdimov  
> Created_at: 2018-05-14 08:57:21 UTC  
> Updated_at: 2018-05-14 12:02:44 UTC  
> Url: https://github.com/boostorg/boost/pull/185#discussion_r187879397  

This line looks wrong?

> Username: grafikrobot  
> Created_at: 2018-05-14 12:01:06 UTC  
> Updated_at: 2018-05-14 12:02:44 UTC  
> Url: https://github.com/boostorg/boost/pull/185#discussion_r187926338  

It is wrong :-(


---

## Comment 4

> Username: grafikrobot  
> Created_at: 2018-05-14 14:05:55 UTC  
> Url: https://github.com/boostorg/boost/pull/185#issuecomment-388829040  

Fixed the stupid mistake. Please merge.

---
