# #600 - Cleaner string digest [Open]

> Username: vinniefalco  
> Created at: 2021-08-04 17:55:05 UTC  
> Updated at: 2021-08-05 16:39:54 UTC  
> Url: https://github.com/boostorg/json/issues/600  

Instead of checking 32/64 bit "arch" macro we can do something like this:  
```  
namespace detail {  
  
template<std::size_t N>  
struct digest_type  
{  
    // Only 4 and 8 byte is supported  
    static_assert(N == 4, "");  
    static std::uint32_t const prime = 0x01000193UL;  
    static std::uint32_t const hash  = 0x811C9DC5UL;  
};  
  
template<>  
struct digest_type<8>  
{  
    static std::uint64_t const prime = 0x100000001B3ULL;  
    static std::uint64_t const hash  = 0xcbf29ce484222325ULL;  
};  
  
} // detail  
  
std::size_t  
idigest(  
    string_view s) noexcept  
{  
    auto p = s.data();  
    auto n = s.size();  
    using T = digest_type<  
        sizeof(std::size_t)>;  
    auto const prime = T::prime;  
    auto hash = T::hash;  
    for(;n--;++p)  
        hash = (to_lower(*p) ^ hash) * prime;  
    return hash;  
}  
```  
  
Note that this version of the function also does lowercasing

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-08-05 16:39:54 UTC  
> Url: https://github.com/boostorg/json/issues/600#issuecomment-893603174  

This is even better:  
```  
    // Only 4 and 8 byte sizes are supported  
    static_assert(  
        sizeof(std::size_t) == 4 ||  
        sizeof(std::size_t) == 8, "");  
    constexpr std::size_t prime = (  
        sizeof(std::size_t) == 8) ?  
            0x100000001B3ULL :  
            0x01000193UL;  
    constexpr std::size_t hash0 = (  
        sizeof(std::size_t) == 8) ?  
            0xcbf29ce484222325ULL :  
            0x811C9DC5UL;  
    auto hash = hash0;  
```
