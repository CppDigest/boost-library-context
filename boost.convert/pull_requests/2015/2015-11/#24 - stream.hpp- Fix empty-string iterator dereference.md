# #24 stream.hpp: Fix empty-string iterator dereference [Merged]

> Username: res2k  
> Created at: 2015-11-21 13:58:59 UTC  
> Updated at: 2015-11-21 22:03:07 UTC  
> Merged at: 2015-11-21 20:32:06 UTC  
> Closed at: 2015-11-21 20:32:06 UTC  
> Url: https://github.com/boostorg/convert/pull/24  

The stream converter apparently bypasses cnvbase::to_str() so there's another place where an empty iterator might be dereferenced.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2015-11-21 20:32:31 UTC  
> Url: https://github.com/boostorg/convert/pull/24#issuecomment-158679195  

stream.hpp: Fix empty-string iterator dereference

---
