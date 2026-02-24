# #264 - to_chars std::float128_t/__float128 [Open]

> Username: gpeterhoff  
> Created at: 2025-05-23 19:02:21 UTC  
> Updated at: 2025-10-04 15:19:19 UTC  
> Url: https://github.com/boostorg/charconv/issues/264  

In many cases, there are differing results or errors compared to std::to_chars, especially when:  
- precision is specified  
- precision >= 35
