# #35 Fix fs::create_directories(".") and fs::create_directories("..") internal assertion failure [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2016-11-09 18:12:00 UTC  
> Updated at: 2016-11-23 15:37:08 UTC  
> Closed at: 2016-11-23 15:37:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/35  

Hi,  
  
I am having regressions between Boost 1.59 and 1.61 when calling fs::create_directories(".") or fs::create_directories(".."). It now fails with:  
Assertion `(parent != p)&&("internal error: p == p.parent_path()")'  
  
This is because "..".parent() is empty, and calling fs::create_directories("") is actually invalid.  
  
Is this OK for the trunk ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: Beman  
> Created_at: 2016-11-23 15:37:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/35#issuecomment-262548859  

Hi Romain,  
  
This problem was also ticket #12495, and was fixed as a result of that ticket. The fix should be in time for the 1.63.0 release.  
  
Thanks,  
  
--Beman

---
