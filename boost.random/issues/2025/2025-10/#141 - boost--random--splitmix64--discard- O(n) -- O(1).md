# #141 - boost::random::splitmix64::discard: O(n) -> O(1) [Closed]

> Username: bddap  
> Created at: 2025-10-23 21:57:14 UTC  
> Updated at: 2025-10-24 06:40:07 UTC  
> Closed at: 2025-10-24 06:40:07 UTC  
> Url: https://github.com/boostorg/random/issues/141  

[`boost::random::splitmix64::discard`](https://github.com/boostorg/random/blob/2d1f612ffaf77a9e05417cb57de8252dd810929a/include/boost/random/splitmix64.hpp#L117) is potentially/probably pokey:  
  
```c++  
    /** Advances the state of the generator by @c z. */  
    inline void discard(std::uint64_t z) noexcept  
    {  
        for (std::uint64_t i {}; i < z; ++i)  
        {  
            next();  
        }  
    }  
```  
  
perhaps pick this more performant procedure  
  
```c++  
    /** Advances the state of the generator by @c z. */  
    inline void discard(std::uint64_t z) noexcept  
    {  
        _state += z * UINT64_C(0x9E3779B97F4A7C15);  
    }  
```

---

## Comment 1

> Username: bddap  
> Created at: 2025-10-23 22:42:03 UTC  
> Url: https://github.com/boostorg/random/issues/141#issuecomment-3439548425  

ps: clang `-O2` and better does seem to figure this one out automatically, `-O1` does not
