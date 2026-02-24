# #345 Add local-visibility feature, mark visibility as a propagated feature [Merged]

> Username: Lastique  
> Created at: 2018-09-30 17:49:31 UTC  
> Updated at: 2021-10-02 21:19:36 UTC  
> Merged at: 2018-10-02 03:02:06 UTC  
> Closed at: 2018-10-02 03:02:06 UTC  
> Url: https://github.com/boostorg/build/pull/345  

This is a result of the discussion in https://github.com/boostorg/build/pull/333.  
  
local-visibility is intended to be used by libraries or targets that require  
a particular visibility mode. It is not propagated to dependencies. It is  
equivalent to the previous visibility feature.  
  
The new visibility feature is a composite propagated feature, so it can be  
specified by users and higher level targets as a requirement. This feature is  
translated to local-visibility.

---
