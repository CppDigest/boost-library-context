# #10 - BOOST_OPENMETHOD: move return type to signature if possible [Closed]

> Username: jll63  
> Created at: 2025-05-06 23:30:34 UTC  
> Updated at: 2025-06-19 21:35:05 UTC  
> Closed at: 2025-06-19 21:32:05 UTC  
> Url: https://github.com/boostorg/openmethod/issues/10  

`BOOST_OPENMETHOD(poke, (std::ostream& a, virtual_ptr<Animal> b) -> void)`

---

## Comment 1

> Username: jll63  
> Created at: 2025-05-19 22:34:40 UTC  
> Url: https://github.com/boostorg/openmethod/issues/10#issuecomment-2892436442  

```c++  
#include <string>  
#include <tuple>  
  
template<typename> struct test{};  
  
using foo = test<auto (int)->std::string>;  
using bar = test<auto (int)->std::tuple<int>>; // MSVC fails on this  
using baz = test<auto (int)->decltype(std::tuple<int>())>;  
```  
  
https://godbolt.org/z/MrbdYbW9Y

---

## Comment 2

> Username: jll63  
> Created at: 2025-05-25 20:50:18 UTC  
> Url: https://github.com/boostorg/openmethod/issues/10#issuecomment-2908073267  

https://github.com/jll63/Boost.OpenMethod/pull/23 for `method` only. Not decided about changing the macros yet.
