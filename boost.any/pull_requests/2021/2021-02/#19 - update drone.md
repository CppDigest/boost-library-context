# #19 update drone [Merged]

> Username: sdarwin  
> Created at: 2021-02-04 15:12:42 UTC  
> Updated at: 2021-04-17 15:01:17 UTC  
> Merged at: 2021-04-17 15:01:10 UTC  
> Closed at: 2021-04-17 15:01:10 UTC  
> Url: https://github.com/boostorg/any/pull/19  

The script generating drone config files from .travis.yml expects to find an "install" section and a "script" section.  This repo has been updated to move "before_install", to "install", so the resulting config is more consolidated and maintainable. You are welcome to rearrange the sections, if necessary.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-04-17 15:01:17 UTC  
> Url: https://github.com/boostorg/any/pull/19#issuecomment-821836369  

Thanks!

---
