# #35 - Odd ellipsis in `constexpr_swap` in `include/boost/integer/common_factor_rt.hpp` [Closed]

> Username: stbergmann  
> Created at: 2024-12-02 07:47:37 UTC  
> Updated at: 2024-12-02 17:21:24 UTC  
> Closed at: 2024-12-02 17:21:24 UTC  
> Url: https://github.com/boostorg/integer/issues/35  

Recent GCC 15 trunk in C++26 mode now warns about  
```  
$ g++ -std=c++23 -fsyntax-only .../include/boost/integer/common_factor_rt.hpp  
.../include/boost/integer/common_factor_rt.hpp:66:56: warning: omission of ‘,’ before varargs ‘...’ is deprecated in C++26 [-Wdeprecated-variadic-comma-omission]  
   66 |          inline constexpr void constexpr_swap(T&a, U& b...) BOOST_GCD_NOEXCEPT(T)  
      |                                                        ^~~  
      |                                                        ,   
```  
And that ellipsis there, introduced in <https://github.com/boostorg/integer/commit/beb68718640150f67fe5671bbbb7848d9e7170b8> "Switch over to using new Boost.Math version of gcd/lcm", looks more like a typo anyway?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-12-02 15:52:17 UTC  
> Url: https://github.com/boostorg/integer/issues/35#issuecomment-2511908257  

It's not a typo, I'm pretty sure it's there to disambiguate function overload resolution.
