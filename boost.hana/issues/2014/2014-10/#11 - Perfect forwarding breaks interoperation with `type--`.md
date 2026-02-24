# #11 - Perfect forwarding breaks interoperation with `type<>` [Closed]

> Username: ldionne  
> Created at: 2014-10-05 00:30:38 UTC  
> Updated at: 2014-11-08 02:40:37 UTC  
> Closed at: 2014-11-08 02:40:37 UTC  
> Url: https://github.com/boostorg/hana/issues/11  

Since we started adding perfect forwarding all around the place, some methods now return references instead of plain values. For example,  
  
``` cpp  
auto types = tuple(type<T>, type<U>, type<V>);  
static_assert(std::is_same<  
    decltype(head(types)),  
    decltype(type<T>)&  
>::value, "");  
```  
  
This causes stuff like `decltype(head(types))::type` to fail, which is a _huge_ problem.
