# #134 Specialize std::hash for json string [Merged]

> Username: vinniefalco  
> Created at: 2020-08-08 00:41:58 UTC  
> Updated at: 2020-08-09 16:08:25 UTC  
> Merged at: 2020-08-09 16:05:51 UTC  
> Closed at: 2020-08-09 16:05:51 UTC  
> Url: https://github.com/boostorg/json/pull/134  

The hash function can be optionally salted, for security.

---

## Review 1 [Commented]

> Username: sdkrystian  
> Created_at: 2020-08-08 00:58:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/134#pullrequestreview-463701498  

📁 include/boost/json/string.hpp

```diff
2952 |+ // std::hash specialization
2953 |+ #ifndef BOOST_JSON_DOCS
2954 |+ namespace std {
```

> Username: sdkrystian  
> Created_at: 2020-08-08 00:48:41 UTC  
> Updated_at: 2020-08-09 16:05:51 UTC  
> Url: https://github.com/boostorg/json/pull/134#discussion_r467339770  

This technically isn't needed, we could just do:  
```cpp  
template<>  
struct std::hash<::boost::json::string> { ... };  
```

---

📁 include/boost/json/string.hpp

```diff
2961 |+ 
2962 |+     explicit
2963 |+     hash(std::size_t salt)
```

> Username: sdkrystian  
> Created_at: 2020-08-08 00:51:12 UTC  
> Updated_at: 2020-08-09 15:39:26 UTC  
> Url: https://github.com/boostorg/json/pull/134#discussion_r467340139  

This must be marked `noexcept` (see http://eel.is/c++draft/unord.hash#3)


📁 include/boost/json/detail/impl/digest.ipp

```diff
  39 |+     std::true_type) noexcept
  40 |+ {
  41 |+     std::uint64_t const prime = 0x100000001B3ULL;
```

> Username: sdkrystian  
> Created_at: 2020-08-08 00:55:15 UTC  
> Updated_at: 2020-08-09 16:05:51 UTC  
> Url: https://github.com/boostorg/json/pull/134#discussion_r467340661  

We could just use `BOOST_JSON_ARCH` to set the constants instead of having two overloads

> Username: sdkrystian  
> Created_at: 2020-08-08 00:57:26 UTC  
> Updated_at: 2020-08-09 16:05:51 UTC  
> Url: https://github.com/boostorg/json/pull/134#discussion_r467340927  

E.g.   
```cpp  
#if BOOST_JSON_ARCH == 64  
std::uint64_t const prime = 0x100000001B3ULL;  
std::uint64_t hash  = 0xcbf29ce484222325ULL;  
#else   
std::uint32_t const prime = 0x01000193UL;  
std::uint32_t hash  = 0x811C9DC5UL;  
#endif   
...  
```


---

## Review 2 [Commented]

> Username: sdkrystian  
> Created_at: 2020-08-08 00:59:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/134#pullrequestreview-463702855  

📁 test/string.cpp

```diff
2652 |+     void
2653 |+     testHash()
2654 |+     {
```

> Username: sdkrystian  
> Created_at: 2020-08-08 00:59:46 UTC  
> Updated_at: 2020-08-09 16:05:51 UTC  
> Url: https://github.com/boostorg/json/pull/134#discussion_r467341228  

These unused variables are causing test failures (`-Wextra`)


---
