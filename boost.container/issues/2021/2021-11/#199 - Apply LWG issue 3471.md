# #199 - Apply LWG issue 3471 [Closed]

> Username: vinniefalco  
> Created at: 2021-11-10 16:13:46 UTC  
> Updated at: 2022-01-06 22:34:08 UTC  
> Closed at: 2022-01-06 22:34:08 UTC  
> Url: https://github.com/boostorg/container/issues/199  

`Allocator::allocate must create objects using launder( new(p) byte[n*sizeof(T)]`  
https://cplusplus.github.io/LWG/issue3471  
  
Change `memory_resource::allocate` to:  
```  
void* p = do_allocate( bytes, alignment );  
return std::launder( new (p) std::byte[ bytes ]);  
```  
  
On the build configurations that need it (C++17?) with suitable ifdefs.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-10 16:20:09 UTC  
> Url: https://github.com/boostorg/container/issues/199#issuecomment-965505738  

Looks like the right one to use is actually `return ::operator new(size, do_allocate(size, align));`

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-11-10 17:43:44 UTC  
> Url: https://github.com/boostorg/container/issues/199#issuecomment-965584659  

I must confess I don't understand yet what launder does. Were concerns raised in [P0532r0](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0532r0.pdf) addressed?  
  
Do we have some kind of BOOST_LAUNDER (calling some kind of "__builtin_launder", I guess) macro that avoids including <new>, which in many cases includes type_traits and other non-trivial dependencies for such a low-level feature?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-11-10 17:49:27 UTC  
> Url: https://github.com/boostorg/container/issues/199#issuecomment-965589399  

Well Peter's comment suggests you don't need `launder`
