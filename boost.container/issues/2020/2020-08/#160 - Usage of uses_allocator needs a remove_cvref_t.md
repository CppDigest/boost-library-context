# #160 - Usage of uses_allocator needs a remove_cvref_t [Closed]

> Username: vinniefalco  
> Created at: 2020-08-29 16:36:02 UTC  
> Updated at: 2020-08-31 20:21:58 UTC  
> Closed at: 2020-08-31 20:21:49 UTC  
> Url: https://github.com/boostorg/container/issues/160  

This specialization of `boost::container::uses_allocator`:  
```  
template<class T>  
struct uses_allocator<  
    ::boost::json::value,  
    json::polymorphic_allocator<T>>  
        : std::true_type  
{  
};  
```  
  
fails, because Boost.Container does not use `remove_cvref` on the allocator. This causes `uses_allocator` to receive `json::polymorphic_allocator<T>&`  
  
However if I use this workaround:  
```  
template<class T>  
struct uses_allocator<  
    ::boost::json::value,  
    json::polymorphic_allocator<T>& > // <---- note the reference  
        : std::true_type  
{  
};  
```  
  
it works correctly. The problem is here (and is likely in other places). I believe `remove_cvref_t<ArgAlloc>` is needed:  
https://github.com/boostorg/container/blob/5a52472cd00994bf1752e92c1c178a5a822a36cb/include/boost/container/detail/dispatch_uses_allocator.hpp#L116

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-08-31 20:21:58 UTC  
> Url: https://github.com/boostorg/container/issues/160#issuecomment-684016854  

Thanks for the report!
