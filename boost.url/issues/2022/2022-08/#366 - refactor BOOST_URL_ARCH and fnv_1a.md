# #366 - refactor BOOST_URL_ARCH and fnv_1a [Closed]

> Username: vinniefalco  
> Created at: 2022-08-07 22:40:33 UTC  
> Updated at: 2022-09-01 22:40:53 UTC  
> Closed at: 2022-09-01 22:40:53 UTC  
> Url: https://github.com/boostorg/url/issues/366  

The HTTP.Proto library shows how to implement fnv_1a for 32 and 64 bit without having to know the architecture. We should adopt that algorithm and do away with the configuration macros (which are probably not 100% reliable anyway)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-08 19:09:20 UTC  
> Url: https://github.com/boostorg/url/issues/366#issuecomment-1208503008  

Example  
```  
std::size_t  
idigest(  
    string_view s) noexcept  
{  
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
    auto p = s.data();  
    auto n = s.size();  
    for(;n--;++p)  
    {  
        // VFALCO NOTE Consider using a lossy  
        // to_lower which works 4 or 8 chars at a time.  
        hash = (to_lower(*p) ^ hash) * prime;  
    }  
    return hash;  
}  
```
