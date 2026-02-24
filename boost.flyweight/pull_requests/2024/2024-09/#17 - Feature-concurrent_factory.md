# #17 Feature/concurrent_factory [Merged]

> Username: joaquintides  
> Created at: 2024-09-21 18:02:57 UTC  
> Updated at: 2024-09-26 17:18:58 UTC  
> Merged at: 2024-09-26 17:18:54 UTC  
> Closed at: 2024-09-26 17:18:55 UTC  
> Url: https://github.com/boostorg/flyweight/pull/17  

Concurrent factory based on `boost::concurrent_node_set`:  
  
* Doesn't need external locking or tracking.  
* Value erasure is not deterministic (i.e. when all asociated flyweights are destroyed), but carried out by a built-in garbage collector.  
  
[Documentation preview](https://html-preview.github.io/?url=https://raw.githubusercontent.com/boostorg/flyweight/feature/concurrent_factory/doc/index.html).

---
