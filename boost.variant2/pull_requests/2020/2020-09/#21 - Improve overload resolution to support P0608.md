# #21 Improve overload resolution to support P0608 [Open]

> Username: jbuonagurio  
> Created at: 2020-09-07 02:50:42 UTC  
> Updated at: 2020-10-29 12:09:13 UTC  
> Url: https://github.com/boostorg/variant2/pull/21  

This PR is an initial attempt at adding support for P0608R3: "A sane variant converting constructor" (https://wg21.link/p0608) so conversions like this should work as expected:  
  
```  
namespace variant2 = boost::variant2;  
variant2::variant<bool, std::string, int, int> v("test");  
assert(variant2::holds_alternative<std::string>(v));  
```  
  
The tradeoff is that user-defined types convertible to `bool` may construct a different type. More information in P1957 (https://wg21.link/p1957)  
   
I'm fairly new to metaprogramming so any feedback on the implementation and/or correct use of Mp11 is much appreciated. I haven't yet added workarounds for the older MSVC versions and it's not yet thoroughly tested, but I can help write some test cases if you're interested in merging.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-10-29 12:09:13 UTC  
> Url: https://github.com/boostorg/variant2/pull/21#issuecomment-718711174  

The reason your `is_arithmetic` check doesn't seem to work is that `mp_invoke_q` is immediately evaluated regardless of the value of `is_arithmetic`. It should probably be  
```  
template<class T, class U> using narrowing_check = mp11::mp_eval_if<std::is_arithmetic<T>, mp11::mp_identity<T>, mp11::mp_invoke_q, narrowing_check_impl, T, U>;  
```  
However, this check doesn't seem necessary; the tests are just wrong for a C++20 variant. E.g. https://godbolt.org/z/KMcnb1

---
