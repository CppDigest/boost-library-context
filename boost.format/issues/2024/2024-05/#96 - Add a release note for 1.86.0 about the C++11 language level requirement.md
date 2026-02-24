# #96 - Add a release note for 1.86.0 about the C++11 language level requirement [Closed]

> Username: jeking3  
> Created at: 2024-05-14 14:23:59 UTC  
> Updated at: 2024-06-05 12:23:07 UTC  
> Closed at: 2024-06-05 12:23:07 UTC  
> Url: https://github.com/boostorg/format/issues/96  

boost::format has a number of dependencies, some of which have moved to only supporting C++11:  
  
1. boost::optional  
2. boost::smart_ptr  
  
Therefore boost::format must join them in only supporting C++11 or later.  
  
We can reduce the complexity of CI builds a bit, dropping older compilers and the 98 and 03 language level builds.

---

## Comment 1

> Username: jeking3  
> Created at: 2024-06-04 21:48:52 UTC  
> Url: https://github.com/boostorg/format/issues/96#issuecomment-2148470567  

https://github.com/boostorg/website/pull/840
