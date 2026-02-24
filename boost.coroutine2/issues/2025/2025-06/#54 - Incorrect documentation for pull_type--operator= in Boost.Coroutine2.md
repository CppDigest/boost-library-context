# #54 - Incorrect documentation for pull_type::operator= in Boost.Coroutine2 [Open]

> Username: kelbon  
> Created at: 2025-06-27 11:33:59 UTC  
> Updated at: 2025-06-27 11:33:59 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/54  

The documentation for pull_type::operator= in Boost.Coroutine2 is currently misleading. It states:  
```  
Effects: Destroys the internal data of *this and moves the internal data of other to *this. other becomes not-a-coroutine.  
```  
  
Real code:  
  
```cpp  
    pull_coroutine & operator=( pull_coroutine && other) noexcept {  
        std::swap( cb_, other.cb_);  
        return * this;  
    }  
```  
  
Additionally, the absence of a .swap() member function in pull_type might lead to incorrect assumptions—particularly that  
```cpp  
  
using std::swap;  
swap(a, b);  
```  
 would invalidate both coroutines, which is not the case.  
  
Doc:  
  
https://www.boost.org/doc/libs/1_78_0/libs/coroutine2/doc/html/coroutine2/coroutine/asymmetric/pull_coro.html
