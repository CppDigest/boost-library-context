# #95 gha: enhance CSS sync workflow with branch support [Merged]

> Username: julioest  
> Created at: 2025-03-14 21:20:00 UTC  
> Updated at: 2025-03-14 22:19:53 UTC  
> Merged at: 2025-03-14 22:19:53 UTC  
> Closed at: 2025-03-14 22:19:53 UTC  
> Url: https://github.com/boostorg/boostlook/pull/95  

This commit improves the boostlook CSS sync workflow:  
  
- add support for the develop branch  
- include boostlook_tino.css in trigger paths  
- implement conditional file copying based on branch  
- restrict ui-release trigger to push events only  
- pass branch name as parameter to downstream workflows

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-03-14 21:29:29 UTC  
> Url: https://github.com/boostorg/boostlook/pull/95#issuecomment-2725815974  

An automated preview of the documentation is available at [https://95.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://95.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---
