# #184 Fix base class offset calculation in void_cast.hpp. [Merged]

> Username: kanje  
> Created at: 2019-12-10 13:05:09 UTC  
> Updated at: 2019-12-12 16:51:44 UTC  
> Merged at: 2019-12-12 16:51:44 UTC  
> Closed at: 2019-12-12 16:51:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/184  

The current approach generates warnings with ASAN and UBSAN as it features  
a magic address which does not hold an object. This change addresses this  
issue. The new code produces the same assembly and does not cause any  
warnings.  
  
Example:  
```  
==72613==ERROR: AddressSanitizer: SEGV on unknown address 0x0000000ffff8  
               (pc 0x0000004012d9 bp 0x7ffd5b3eecf0 sp 0x7ffd5b3eece0 T0)  
```

---
