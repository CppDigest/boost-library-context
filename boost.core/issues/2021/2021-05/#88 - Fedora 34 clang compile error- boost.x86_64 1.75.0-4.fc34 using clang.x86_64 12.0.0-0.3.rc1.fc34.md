# #88 - Fedora 34 clang compile error:  boost.x86_64 1.75.0-4.fc34   using   clang.x86_64 12.0.0-0.3.rc1.fc34 [Closed]

> Username: roberthendrie  
> Created at: 2021-05-11 15:51:26 UTC  
> Updated at: 2021-05-11 17:46:29 UTC  
> Closed at: 2021-05-11 17:46:29 UTC  
> Url: https://github.com/boostorg/core/issues/88  

/usr/include/boost/core/noinit_adaptor.hpp:58:16: error: expected a class name after '~' to name a destructor  
        p->~U();  
               ^  
  
  
Code snippet from noinit_adaptor.hpp  
  
    template<class U>  
    void destroy(U* p) {  
        p->~U();  
    }

---

## Comment 1

> Username: mclow  
> Created at: 2021-05-11 16:25:13 UTC  
> Url: https://github.com/boostorg/core/issues/88#issuecomment-838782989  

If I had to make a guess, I'd guess you have a macro named `U` defined somewhere.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-05-11 17:46:29 UTC  
> Url: https://github.com/boostorg/core/issues/88#issuecomment-838898178  

Duplicates https://github.com/boostorg/core/issues/81.
