# #35 - __see_below__ in cleanup-type [Closed]

> Username: vinniefalco  
> Created at: 2019-01-01 20:51:23 UTC  
> Updated at: 2020-11-10 01:00:07 UTC  
> Closed at: 2020-11-10 01:00:07 UTC  
> Url: https://github.com/boostorg/docca/issues/35  

It would be nice if we could do this (without too much hassle in the XSLT)  
```  
#if BOOST_BEAST_DOXYGEN  
template<class T>  
struct is_async_read_stream : std::integral_constant<bool, __see_below__>{};  
...  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-10 00:58:57 UTC  
> Updated at: 2020-11-10 00:59:25 UTC  
> Url: https://github.com/boostorg/docca/issues/35#issuecomment-724379363  

template<class T>  
struct is_async_read_stream : std::integral_constant<bool, _see-below_ >{};  
  
template<class T>  
struct is_async_read_stream : _see-below_ {};

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-11-10 01:00:07 UTC  
> Url: https://github.com/boostorg/docca/issues/35#issuecomment-724379728  

I don't like how this looks. The workaround (which works now) is:  
```  
template<class T>  
using is_async_read_stream = __see_below__;  
```
