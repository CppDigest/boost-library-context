# #164 merge develop to master for release [Merged]

> Username: jzmaddock  
> Created at: 2019-02-24 08:34:27 UTC  
> Updated at: 2019-04-29 08:02:19 UTC  
> Merged at: 2019-04-28 13:54:05 UTC  
> Closed at: 2019-04-28 13:54:06 UTC  
> Url: https://github.com/boostorg/graph/pull/164  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-02-24 08:35:16 UTC  
> Url: https://github.com/boostorg/graph/pull/164#issuecomment-466748242  

Please comment here if there's anything that should not go to master.  
  
If someone could prepare some release notes that would be super-useful too! :)

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-04-28 20:40:09 UTC  
> Url: https://github.com/boostorg/graph/pull/164#issuecomment-487413536  

You have to merge `graph_parallel` too because of  
  
```  
error: Cannot create link boost/pending/property_serialize.hpp to libs/graph/include/boost/pending/property_serialize.hpp.  
error: Link previously defined to another file, libs/graph_parallel/include/boost/pending/property_serialize.hpp.  
```

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-04-29 08:02:19 UTC  
> Url: https://github.com/boostorg/graph/pull/164#issuecomment-487484422  

>You have to merge graph_parallel too  
  
Oh shucks :(  
  
Thankfully the changes there are all trivial: merged.

---
