# #222 Fix incomplete type error when using list with pair [Closed]

> Username: orgads  
> Created at: 2022-06-06 12:41:52 UTC  
> Updated at: 2022-06-25 18:25:10 UTC  
> Closed at: 2022-06-25 17:40:19 UTC  
> Url: https://github.com/boostorg/container/pull/222  

Example:  
  
```  
boost::container::list<std::pair<int, int>> list;  
```  
```  
.../boost/container/allocator_traits.hpp:403:11: error: invalid use of incomplete type 'struct boost::container::dtl::pair<int, int>'  
  403 |    {  p->~T(); (void)p;  }  
      |       ~~~~^  
In file included from .../boost/container/detail/node_alloc_holder.hpp:39,  
                 from .../boost/container/list.hpp:34:  
.../boost/container/detail/is_pair.hpp:59:8: note: declaration of 'struct boost::container::dtl::pair<int, int>'  
   59 | struct pair;  
      |        ^~~~  
```

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2022-06-25 17:27:33 UTC  
> Url: https://github.com/boostorg/container/pull/222#issuecomment-1166330003  

Thanks for the patch. I'm applying and alternative patch as more containers (like slist) are affected by the same bug.

---
