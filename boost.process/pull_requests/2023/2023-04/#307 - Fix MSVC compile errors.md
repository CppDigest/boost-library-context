# #307 Fix MSVC compile errors [Merged]

> Username: nikola-sh  
> Created at: 2023-04-23 13:13:43 UTC  
> Updated at: 2023-06-13 12:44:07 UTC  
> Merged at: 2023-06-13 12:44:03 UTC  
> Closed at: 2023-06-13 12:44:03 UTC  
> Url: https://github.com/boostorg/process/pull/307  



---

## Review 1 [Commented]

> Username: nikola-sh  
> Created_at: 2023-04-23 13:14:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/307#pullrequestreview-1396908297  

📁 include/boost/process/v2/environment.hpp

```diff
 502 |     template< class Source >
 503 |     key( const Source& source,
 504 |-         decltype(source.data()) = nullptr,
```

> Username: nikola-sh  
> Created_at: 2023-04-23 13:14:14 UTC  
> Updated_at: 2023-04-23 13:15:19 UTC  
> Url: https://github.com/boostorg/process/pull/307#discussion_r1174579332  

`error C2065: 'source': undeclared identifier`


---

## Review 2 [Commented]

> Username: nikola-sh  
> Created_at: 2023-04-23 13:15:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/307#pullrequestreview-1396908425  

📁 include/boost/process/v2/environment.hpp

```diff
1744 |   }
1745 | 
1746 |-   process_environment(std::initializer_list<string_view> sv)  : unicode_env{build_env(sv,  "")} {}
```

> Username: nikola-sh  
> Created_at: 2023-04-23 13:15:10 UTC  
> Url: https://github.com/boostorg/process/pull/307#discussion_r1174579478  

```  
error C2664: 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env<std::initializer_list<boost::string_view>&>(Args,void *)':  
cannot convert argument 2 from 'const char [1]' to 'void *'  
```


---

## Comment 3

> Username: Tectu  
> Created_at: 2023-04-25 20:52:44 UTC  
> Url: https://github.com/boostorg/process/pull/307#issuecomment-1522400767  

I encountered the same build issue using MinGW (via MSYS2).  
I can confirm that this patch fixes the compilation errors.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-06-13 12:44:07 UTC  
> Url: https://github.com/boostorg/process/pull/307#issuecomment-1589240739  

Thanks!

---
