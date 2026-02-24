# #16 Inheriting std::iterator is deprecated in c++17. [Merged]

> Username: DanielaE  
> Created at: 2017-12-27 15:15:20 UTC  
> Updated at: 2018-09-06 03:42:38 UTC  
> Merged at: 2018-09-06 03:42:38 UTC  
> Closed at: 2018-09-06 03:42:38 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/16  

Boost's iterator.hpp is deprecated, too. Therefore get rid of all of that and replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: rcdailey  
> Created_at: 2018-08-20 15:10:55 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/16#issuecomment-414351817  

It's been 8 months since this PR was opened, and no feedback from the developers. Can someone review and merge this? I'm hitting warnings about this on MSVC 15.8 with C++17 enabled.

---

## Comment 2

> Username: glenfe  
> Created_at: 2018-09-06 03:42:28 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/16#issuecomment-418954654  

The original author who is also listed as the current maintainer isn't around. Patch looks good to me, though.

---
