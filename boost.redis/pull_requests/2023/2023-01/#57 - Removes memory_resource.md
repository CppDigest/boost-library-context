# #57 Removes memory_resource. [Merged]

> Username: mzimbres  
> Created at: 2023-01-29 19:26:37 UTC  
> Updated at: 2023-08-21 22:10:22 UTC  
> Merged at: 2023-01-29 20:14:38 UTC  
> Closed at: 2023-01-29 20:14:38 UTC  
> Url: https://github.com/boostorg/redis/pull/57  



---

## Comment 1

> Username: mrichmon  
> Created_at: 2023-08-18 17:31:34 UTC  
> Url: https://github.com/boostorg/redis/pull/57#issuecomment-1684223852  

What is the next planned release that will include this fix?

---

## Comment 2

> Username: mzimbres  
> Created_at: 2023-08-18 20:43:56 UTC  
> Url: https://github.com/boostorg/redis/pull/57#issuecomment-1684417565  

Most users seem to be using the develop branch so I did not bother merging in master and creating a new release. I will prepare a new pre-boost release this weekend.

---

## Comment 3

> Username: mzimbres  
> Created_at: 2023-08-18 21:34:30 UTC  
> Url: https://github.com/boostorg/redis/pull/57#issuecomment-1684457631  

Just made a release tarball v1.4.2. Docs are not updated yet.

---

## Comment 4

> Username: mrichmon  
> Created_at: 2023-08-21 16:04:20 UTC  
> Url: https://github.com/boostorg/redis/pull/57#issuecomment-1686608163  

Thank you.  
The companies I've worked with require release checkpoints for open source projects so this release helps.

---

## Comment 5

> Username: mrichmon  
> Created_at: 2023-08-21 22:10:22 UTC  
> Url: https://github.com/boostorg/redis/pull/57#issuecomment-1687111626  

Note that `CMakeLists.txt` still has "VERSION 1.4.1" in this 1.4.2 tarball.

---
