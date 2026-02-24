# #22 Remove dependencies on Boost.StaticAssert [Merged]

> Username: Lastique  
> Created at: 2026-01-22 16:57:18 UTC  
> Updated at: 2026-01-24 17:55:35 UTC  
> Merged at: 2026-01-24 07:39:11 UTC  
> Closed at: 2026-01-24 07:39:11 UTC  
> Url: https://github.com/boostorg/metaparse/pull/22  

Boost.StaticAssert has been merged into Boost.Config, so remove the dependency.

---

## Comment 1

> Username: Lastique  
> Created_at: 2026-01-24 12:15:00 UTC  
> Updated_at: 2026-01-24 12:16:18 UTC  
> Url: https://github.com/boostorg/metaparse/pull/22#issuecomment-3794543632  

@sabel83 Oh, sorry, this PR was meant to be against develop. Could you please merge it to develop as well?  
  
PS: Boost libraries typically set the default branch to develop, but this library sets it to master, which is likely why I made the mistake. It may be a good idea to change the default branch to develop.

---

## Comment 2

> Username: sabel83  
> Created_at: 2026-01-24 17:20:20 UTC  
> Url: https://github.com/boostorg/metaparse/pull/22#issuecomment-3795148714  

I've pushed the same on the develop branch and changed the default.

---

## Comment 3

> Username: Lastique  
> Created_at: 2026-01-24 17:55:35 UTC  
> Url: https://github.com/boostorg/metaparse/pull/22#issuecomment-3795273324  

Thanks!

---
