# #212 - static_vector::operator== should be constexpr [Open]

> Username: HazardyKnusperkeks  
> Created at: 2022-02-28 21:37:35 UTC  
> Updated at: 2025-03-19 14:32:50 UTC  
> Url: https://github.com/boostorg/container/issues/212  

We never allocate so everything can easily be done in `constexpr`, or do I miss something?

---

## Comment 1

> Username: timblechmann  
> Created at: 2023-10-20 02:43:18 UTC  
> Url: https://github.com/boostorg/container/issues/212#issuecomment-1771989697  

actually, i wonder if all of `static_vector` could  potentially be `constexpr`

---

## Comment 2

> Username: localspook  
> Created at: 2024-05-23 11:33:54 UTC  
> Url: https://github.com/boostorg/container/issues/212#issuecomment-2126885290  

> actually, i wonder if all of static_vector could potentially be constexpr  
  
It can be! There's a `static_vector` [standards proposal](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0843r2.html) and a [reference implementation](https://github.com/gnzlbg/static_vector), all `constexpr`.

---

## Comment 3

> Username: gast128  
> Created at: 2025-03-19 14:29:12 UTC  
> Updated at: 2025-03-19 14:32:49 UTC  
> Url: https://github.com/boostorg/container/issues/212#issuecomment-2736854944  

Indeed if possible make constructors constexpr as well so that one can e.g. write:  
`  
inline constexpr boost::container::static_vector<int, 4> g_vec{1, 2, 3, 4};  
`
