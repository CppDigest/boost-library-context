# #534 - Add detection for C++17 constexpr addressof [Closed]

> Username: akrzemi1  
> Created at: 2026-01-31 23:49:27 UTC  
> Updated at: 2026-02-07 22:27:29 UTC  
> Closed at: 2026-02-01 11:25:18 UTC  
> Url: https://github.com/boostorg/config/issues/534  

Some libraries, like `optional`, can be implemented `constexpr` only if `addressof` is `constexpr`. If it is not I need to apply a workaround:  
* For types that do not provide overloaded unary operator `&`, implement my own `constexpr` `addressof`,  
* For other types, go with non-`constexpr` `std::addressof`.  
  
It would be helpful to easily detect, which approach needs to be applied.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2026-02-01 10:32:16 UTC  
> Url: https://github.com/boostorg/config/issues/534#issuecomment-3830833543  

__cpp_lib_addressof_constexpr ?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2026-02-01 11:03:04 UTC  
> Url: https://github.com/boostorg/config/issues/534#issuecomment-3830883278  

It is my understanding that in order to use it (prior to C++20's header `<version>`), I would have to include header `<memory>` and drag all its stuff in.  
  
I also see this remark in [Boost.Config docs](https://www.boost.org/doc/libs/latest/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_that_allow_use_of_c__14_features_with_c__11_or_earlier_compilers):  
  
> Note that none of the above checks take into account any standard library SD6 feature test macros, as doing so would require `#include`ing almost the whole standard library.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2026-02-01 11:25:18 UTC  
> Url: https://github.com/boostorg/config/issues/534#issuecomment-3830908201  

Ok, I have just realized that there is a `boost::addressof` in Boost.Core: https://github.com/boostorg/core/blob/develop/include/boost/core/addressof.hpp.  
  
I should have reached out to that library.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2026-02-07 22:27:29 UTC  
> Url: https://github.com/boostorg/config/issues/534#issuecomment-3865653199  

One more question, though. What is the criterion for adding a new macro to Boost.Config versus referring people to feature-test macros? I am now in need of one that matches `__cpp_guaranteed_copy_elision`. Can I file an issue for it here? I can see Boost.Config macros for up to C++23.
