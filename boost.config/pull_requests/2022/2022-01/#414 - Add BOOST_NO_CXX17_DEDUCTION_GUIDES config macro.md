# #414 Add BOOST_NO_CXX17_DEDUCTION_GUIDES config macro [Closed]

> Username: Lastique  
> Created at: 2022-01-20 21:27:15 UTC  
> Updated at: 2022-06-05 17:16:31 UTC  
> Closed at: 2022-06-05 17:14:10 UTC  
> Url: https://github.com/boostorg/config/pull/414  

The macro allows to detect whether the compiler supports C++17 class template argument deduction (CTAD) guides.  
  
This macro is needed at least in Boost.Atomic.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-03-06 11:25:08 UTC  
> Url: https://github.com/boostorg/config/pull/414#issuecomment-1059944231  

Sorry for being late coming to this, it's a good idea, but since the logic is the same everywhere, should we not just add the logic once to suffix.hpp and leave the individual compiler configs unchanged (if there are compiler bugs which need workarounds we can always over ride at the individual compiler level).

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-06-05 17:14:09 UTC  
> Url: https://github.com/boostorg/config/pull/414#issuecomment-1146850960  

Superseded by https://github.com/boostorg/config/pull/433

---
