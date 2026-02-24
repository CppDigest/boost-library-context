# #12 Replace operator& with std::addressof in get_if() [Open]

> Username: akrzemi1  
> Created at: 2019-04-10 21:24:31 UTC  
> Updated at: 2019-10-16 19:03:27 UTC  
> Url: https://github.com/boostorg/variant2/pull/12  

Otherwise `get_if` fails to compile for types that overload `operator&` in nasty ways.

---

## Comment 1

> Username: glenfe  
> Created_at: 2019-10-16 19:03:27 UTC  
> Url: https://github.com/boostorg/variant2/pull/12#issuecomment-542846250  

This will break constexpr in C++11 and C++14 because `std::addressof` wasn't constexpr yet.

---
