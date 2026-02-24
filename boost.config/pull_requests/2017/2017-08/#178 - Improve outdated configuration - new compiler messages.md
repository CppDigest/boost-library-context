# #178 Improve outdated configuration / new compiler messages. [Merged]

> Username: jzmaddock  
> Created at: 2017-08-17 16:52:22 UTC  
> Updated at: 2019-11-02 11:18:24 UTC  
> Merged at: 2017-08-18 17:05:22 UTC  
> Closed at: 2017-08-18 17:05:22 UTC  
> Url: https://github.com/boostorg/config/pull/178  



---

## Review 1 [Commented]

> Username: jhunold  
> Created_at: 2017-08-18 06:35:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/178#pullrequestreview-57120608  

📁 include/boost/config/compiler/sunpro_cc.hpp

```diff
 203 | #endif
 204 | //
 205 | // last known and checked version is 0x590:
```

> Username: jhunold  
> Created_at: 2017-08-18 06:35:03 UTC  
> Updated_at: 2017-08-18 07:04:07 UTC  
> Url: https://github.com/boostorg/config/pull/178#discussion_r133888506  

The comment says the last supported version is "0x590" whereas the new code adjusts this to 0x5150. Maybe just omit the version from the comment or delete it?


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-08-18 07:05:02 UTC  
> Url: https://github.com/boostorg/config/pull/178#issuecomment-323276998  

> The comment says the last supported version is "0x590" whereas the new   
> code adjusts this to 0x5150. Maybe just omit the version from the   
> comment or delete it?  
>  
  
IMO the code is self explanatory, so I've removed the version number   
from the comment.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
