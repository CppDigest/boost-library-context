# #21 Removed BOOST_NOEXCEPT from a method that throws [Merged]

> Username: giacomodrago  
> Created at: 2015-04-19 18:31:04 UTC  
> Updated at: 2015-04-22 15:43:41 UTC  
> Merged at: 2015-04-19 18:54:34 UTC  
> Closed at: 2015-04-19 18:54:34 UTC  
> Url: https://github.com/boostorg/coroutine/pull/21  

It was causing the following to crash:  
  
```  
#include <boost/coroutine/all.hpp>  
#include <iostream>  
  
void coroutine(boost::coroutines::symmetric_coroutine<void>::yield_type& yield)  
{  
    yield();  
}  
  
int main()  
{  
    {  
        boost::coroutines::symmetric_coroutine<void>::call_type call(coroutine);  
        call();  
    }  
    std::cout << "This won't be printed" << std::endl;  
  
    return 0;  
}  
```  
  
when compiling with gcc 4.9.0 with the option `-std=c++11`.  
  
I apologise if this is intended behaviour and what happens is caused by misuse of the library from my side.

---

## Comment 1

> Username: olk  
> Created_at: 2015-04-19 18:54:31 UTC  
> Url: https://github.com/boostorg/coroutine/pull/21#issuecomment-94305083  

Thx

---
