# #77 - Improve compilation speed by taking advantage of memoization [Open]

> Username: jonathanpoelen  
> Created at: 2023-04-12 00:53:39 UTC  
> Updated at: 2023-06-04 12:41:20 UTC  
> Url: https://github.com/boostorg/mp11/issues/77  

Mp11 does not take full advantage of memoization, which increases compile time and memory usage.  
  
For example for `mp_map_find_impl`:  
  
```cpp  
template<template<class...> class M, class... T, class K> struct mp_map_find_impl<M<T...>, K>  
{  
    using U = mp_inherit<mpmf_wrap<T>...>;  
  
    template<template<class...> class L, class... U> static mp_identity<L<K, U...>> f( mp_identity<L<K, U...>>* );  
    static mp_identity<void> f( ... );  
  
    using type = mpmf_unwrap< decltype( f((U*)0) ) >;  
};  
```  
  
Here, the function `f` is duplicated as many times as there are instantiations of `mp_map_find_impl`. but, `f` depends only on `K`, not on `M`, nor on `T`. Moving the functions into a separate structure that takes `K` allows it not to be duplicated.  
  
Example:  
  
```cpp  
template<class K> struct mp_map_find_key  
{  
    template<template<class...> class L, class... U> static mp_identity<L<K, U...>> f( mp_identity<L<K, U>  
    static mp_identity<void> f( ... );  
};  
  
template<template<class...> class M, class... T, class K> struct mp_map_find_impl<M<T...>, K>  
{  
    using U = mp_inherit<mpmf_wrap<T>...>;  
  
    using type = mpmf_unwrap< decltype( mp_map_find_key<K>::f((U*)0) ) >;  
};  
```  
  
Here is the before/after result (measured with `/usr/bin/time --format='%Es - %MK'` on Linux)  
  
compiler | gcc-12 | clang-15  
-- | -- | --  
before | 0:00.46s - 200544K | 0:00.45s - 159492K  
after | 0:00.37s - 172132K | 0:00.43s - 156836K  
  
The test code:  
  
```cpp  
#include <boost/mp11/map.hpp>  
  
using namespace boost::mp11;  
  
using numbers = mp_iota_c<100>;  
using l1 = mp_transform<mp_list, mp_append<numbers, numbers>>;  
using l2 = mp_append<mp_list<mp_list<mp_size_t<2>>>, l1>;  
using l3 = mp_append<mp_list<mp_list<mp_size_t<3>>>, l1>;  
using l4 = mp_append<mp_list<mp_list<mp_size_t<4>>>, l1>;  
using l5 = mp_append<mp_list<mp_list<mp_size_t<5>>>, l1>;  
  
using m1 = mp_fold<l1, mp_list<>, mp_map_insert>;  
using m2 = mp_fold<l2, mp_list<>, mp_map_insert>;  
using m3 = mp_fold<l3, mp_list<>, mp_map_insert>;  
using m4 = mp_fold<l4, mp_list<>, mp_map_insert>;  
using m5 = mp_fold<l5, mp_list<>, mp_map_insert>;  
```  
  
I noticed this pattern several times, but the changes are minor compared to the benefit (especially with gcc).

---

## Comment 1

> Username: pdimov  
> Created at: 2023-05-16 16:03:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/77#issuecomment-1549956153  

Would you be willing to submit this change as a pull request?

---

## Comment 2

> Username: jonathanpoelen  
> Created at: 2023-06-04 12:41:20 UTC  
> Url: https://github.com/boostorg/mp11/issues/77#issuecomment-1575554996  

Yes, I can do that within a month. I'd also do it for the other functions that use the same pattern.
