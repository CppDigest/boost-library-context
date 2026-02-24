# #89 - Maybe add mp_set_find [Open]

> Username: rolandschulz  
> Created at: 2024-01-07 08:33:12 UTC  
> Updated at: 2024-01-08 16:19:42 UTC  
> Url: https://github.com/boostorg/mp11/issues/89  

In cases a list is a set it is possible to do `mp_find` quicker. Is it worth to add this?   
```c++  
template <class S>  
using mp_enumerate = mp_transform<mp_list, S, mp_iota<mp_size<S>>>;  
template <class S, class V>  
using mp_set_find = mp_second<mp_map_find<mp_enumerate<S>, V>>;  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-01-07 15:22:15 UTC  
> Url: https://github.com/boostorg/mp11/issues/89#issuecomment-1880088617  

Probably makes sense for completeness. I wonder what's the actual difference in performance in 2024 compared to `mp_find`, though.

---

## Comment 2

> Username: rolandschulz  
> Created at: 2024-01-08 01:46:19 UTC  
> Url: https://github.com/boostorg/mp11/issues/89#issuecomment-1880285713  

The speedup isn't huge with modern gcc/clang. With icx 2024.1 I get for  
```c++  
using S = mp_iota_c<100>;  
  
template <class S>  
using mp_enumerate = mp_transform<mp_list, S, mp_iota<mp_size<S>>>;  
  
using E = mp_enumerate<S>;  
  
template <class S, class V>  
using mp_set_find = mp_second<mp_map_find<E, V>>;  
// using mp_set_find = mp_find<S,V>;  
  
template<class A, class B> using F = mp_less<mp_set_find<S, A>, mp_set_find<S, B>>;  
static_assert(std::is_same_v<mp_sort<S, F>, S>);  
```  
1.6s for mp_set_find and 2.2s for mp_find.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-01-08 16:19:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/89#issuecomment-1881397673  

I think this is an improvement that justifies having `mp_set_find`.  
  
I'd like to add `mp_enumerate` as well, but most other languages (and `std::views::enumerate`) put the index first, not second. (Which is actually what `mp_at` uses.)
