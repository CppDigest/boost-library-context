# #341 - make_ready_future<void> bug [Open]

> Username: dvvn  
> Created at: 2021-05-25 20:10:24 UTC  
> Updated at: 2021-05-25 20:10:24 UTC  
> Url: https://github.com/boostorg/thread/issues/341  

Used boost version 1.76.0  
  
next code from future.hpp, line 4388  
promise\<void\> have no emplace method  
```  
 // variadic overload  
#if ! defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES)  
  template <class T, class ...Args>  
  BOOST_THREAD_FUTURE<T> make_ready_future(Args&&... args)  
  {  
    promise<T> p;  
    p.emplace(forward<Args>(args)...);  
    return p.get_future();  
  
  }  
#endif  
```  
  
header contains correct function for promise\<void\>, but compiler always select variadic-templated method instead of normal
