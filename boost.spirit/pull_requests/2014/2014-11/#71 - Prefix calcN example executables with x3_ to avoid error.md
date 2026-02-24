# #71 Prefix calcN example executables with x3_ to avoid error. [Merged]

> Username: mlang  
> Created at: 2014-11-20 20:18:03 UTC  
> Updated at: 2014-11-20 22:22:44 UTC  
> Merged at: 2014-11-20 22:22:44 UTC  
> Closed at: 2014-11-20 22:22:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/71  

At least on Linux, b2 fails to link calc{7,8,9} since the exe name is  
identical to the subdirectory containing the source files.  
  
Prefix all calc examples with x3_ to actually allow to link them.

---
