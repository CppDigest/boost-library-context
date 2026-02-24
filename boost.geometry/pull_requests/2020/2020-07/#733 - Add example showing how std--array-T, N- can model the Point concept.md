# #733 Add example showing how std::array<T, N> can model the Point concept [Merged]

> Username: BenFrantzDale  
> Created at: 2020-07-08 13:57:32 UTC  
> Updated at: 2020-08-24 10:39:06 UTC  
> Merged at: 2020-08-24 10:39:06 UTC  
> Closed at: 2020-08-24 10:39:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/733  

This is the documentation I found to be missing. I posted something like it at https://stackoverflow.com/a/62777496/874660 but switched this example to `std::array<T,N>` to be more standard.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2020-07-08 15:44:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/733#issuecomment-655600270  

I think it's a good idea to provide an example in the documentation.  
  
This example is written in newer standard than the one we currently support. We're moving to the newer one in Boost ver 1.75. So if you prefer it to be included in 1.74 the example should be rewritten. Otherwise we should wait with merging.  
  
Regarding the example at SO, you wrote that the example is in C++17. Why is that? I can only see C++11. Which feature am I missing?

---

## Comment 2

> Username: BenFrantzDale  
> Created_at: 2020-07-08 22:18:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/733#issuecomment-655786780  

> This example is written in newer standard than the one we currently support. We're moving to the newer one in Boost ver 1.75. So if you prefer it to be included in 1.74 the example should be rewritten. Otherwise we should wait with merging.  
  
My day job is all C++17 so I'm rusty on what showed up when, but `namespace boost::geometry::traits {` vs `namespace boost { namespace geometry { namespace traits {` is post-C++11. (Looks like it's C++17 https://en.cppreference.com/w/cpp/language/namespace) That may be the only thing. I just rewrote it with the expanded C++11-compatible namespaces.  
  
Also, I'm not familiar with `.qbk` files. I assumed triple back-tick is a code block.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-07-08 23:20:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/733#issuecomment-655807070  

Ah right, nested namespaces. I didn't even look at them. Thanks for the explanation!  
  
Btw, you could put them in one line. We use this pattern in the library:  
```  
namespace boost { namespace geometry { namespace traits  
{  
// ...  
}}} // namespace boost::geometry::traits  
```  
If you prefer it like it is now I'm also fine with that.  
  
Ok, so since you decided to use C++11 I understand that we're waiting for 1.75.

---

## Comment 4

> Username: BenFrantzDale  
> Created_at: 2020-07-08 23:30:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/733#issuecomment-655810149  

I’m fine with whatever style you like. Just let me know and I’ll change it.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-07-09 12:10:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/733#issuecomment-656089909  

I'd prefer all of the namespaces in one line (as I show above) but let's wait with the change for other opinions.

---

## Review 6 [Commented]

> Username: vissarion  
> Created_at: 2020-07-10 08:45:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/733#pullrequestreview-446229380  

📁 doc/concept/point.qbk

```diff
  36 |+ [heading Example]
  37 |+ 
  38 |+ While you can `#include boost/geometry/geometries/adapted/std_array.hpp` to use `std::array<T, D>` to model the Point concept, the following code spells it out in detail, as you ight use for your own point types:
```

> Username: vissarion  
> Created_at: 2020-07-10 08:45:57 UTC  
> Updated_at: 2020-07-10 12:59:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/733#discussion_r452709272  

`ight` -> `might`


---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2020-07-10 08:47:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/733#pullrequestreview-446230403  

Seems ok to me, thanks!

---

## Comment 8

> Username: awulkiew  
> Created_at: 2020-08-24 10:39:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/733#issuecomment-679051144  

Thanks again!

---
