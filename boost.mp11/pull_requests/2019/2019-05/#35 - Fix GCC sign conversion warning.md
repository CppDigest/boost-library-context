# #35 Fix GCC sign conversion warning [Merged]

> Username: foonathan  
> Created at: 2019-05-22 09:04:50 UTC  
> Updated at: 2019-05-22 13:19:57 UTC  
> Merged at: 2019-05-22 13:19:57 UTC  
> Closed at: 2019-05-22 13:19:57 UTC  
> Url: https://github.com/boostorg/mp11/pull/35  

The conversion bool -> int -> std::size_t triggers GCC sign conversion warning.  
  
I don't know your policy about those warnings. Alternatively the CMake include path can be specified as SYSTEM.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-05-22 11:22:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/35#issuecomment-494761528  

Would you please give me a way to reproduce these warnings?

---

## Comment 2

> Username: foonathan  
> Created_at: 2019-05-22 11:53:07 UTC  
> Url: https://github.com/boostorg/mp11/pull/35#issuecomment-494770382  

```  
using list = mp_list<double, int, int, int, int, int, int, int, int, int>; // at least 10  
constexpr auto count = mp_count<list, double>::value;  
```  
  
Compile with `g++ -std=c++14 -Wsign-conversion` (important: fold expression not supported):  
  
```  
In file included from ./boost/mp11/function.hpp:14,  
                 from ./boost/mp11/set.hpp:12,  
                 from ./boost/mp11/algorithm.hpp:12,  
                 from boost/mp11.hpp:12,  
                 from file.cpp:1:  
./boost/mp11/detail/mp_count.hpp: In instantiation of ‘constexpr std::size_t boost::mp11::detail::cx_plus(T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T ...) [with T1 = bool; T2 = bool; T3 = bool; T4 = bool; T5 = bool; T6 = bool; T7 = bool; T8 = bool; T9 = bool; T10 = bool; T = {}; std::size_t = long unsigned int]’:  
./boost/mp11/detail/mp_count.hpp:57:43:   required by substitution of ‘template<long unsigned int N> using mp_size_t = std::integral_constant<long unsigned int, N> [with long unsigned int N = boost::mp11::detail::cx_plus<bool, bool, bool, bool, bool, bool, bool, bool, bool, bool>(((int)((bool)std::integral_constant<bool, true>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)), ((int)((bool)std::integral_constant<bool, false>::value)))]’  
./boost/mp11/detail/mp_count.hpp:57:74:   required from ‘struct boost::mp11::detail::mp_count_impl<boost::mp11::mp_list<double, int, int, int, int, int, int, int, int, int>, double>’  
./boost/mp11/detail/mp_count.hpp:73:64:   required by substitution of ‘template<class L, class V> using mp_count = typename boost::mp11::detail::mp_count_impl::type [with L = boost::mp11::mp_list<double, int, int, int, int, int, int, int, int, int>; V = double]’  
file.cpp:9:49:   required from here  
./boost/mp11/detail/mp_count.hpp:51:63: warning: conversion to ‘std::size_t’ {aka ‘long unsigned int’} from ‘int’ may change the sign of the result [-Wsign-conversion]  
             return t1 + t2 + t3 + t4 + t5 + t6 + t7 + t8 + t9 + t10 + cx_plus(t...);  
                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~  
```

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-05-22 12:17:52 UTC  
> Url: https://github.com/boostorg/mp11/pull/35#issuecomment-494777823  

Interesting. I don't get the warning on godbolt.org, tried with all GCC versions down to 4.9. https://godbolt.org/z/J8xyI1

---

## Comment 4

> Username: foonathan  
> Created_at: 2019-05-22 12:54:56 UTC  
> Url: https://github.com/boostorg/mp11/pull/35#issuecomment-494789762  

On godbolt Boost is added as system include which suppresses warnings.

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-05-22 13:19:45 UTC  
> Url: https://github.com/boostorg/mp11/pull/35#issuecomment-494798750  

Indeed, thanks.

---
