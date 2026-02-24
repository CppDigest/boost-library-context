# #36 - boost::hash needs c++20 to compile with clang 15 and gcc 10 [Closed]

> Username: martinsjogren  
> Created at: 2024-03-05 09:59:57 UTC  
> Updated at: 2024-03-06 09:52:56 UTC  
> Closed at: 2024-03-06 08:31:36 UTC  
> Url: https://github.com/boostorg/container_hash/issues/36  

I have a blob type with begin/end member functions that return const void* and when I try to use that with boost::unordered_map I get a compilation error with gcc 10 and clang 15 unless I compile with -std=c++20 (funny enough it works with gcc 11+ and msvc)  
  
Am I doing something strange or are the ADL changes in c++20 relevant for this case? I attached a small repro as well as the output from compiling with gcc-10 -std=c++17 and clang-15 -std=c++17  
  
Either way it feels like boost shouldn't require c++20 to compile this example where I provide my own hash_value function, right? I'm really confused whether this is a bug in boost or a bug somewhere in gcc/clang or if my code is doing something wrong.  
  
[hash_repro.tar.gz](https://github.com/boostorg/container_hash/files/14493465/hash_repro.tar.gz)

---

## Comment 1

> Username: pdimov  
> Created at: 2024-03-05 10:28:17 UTC  
> Url: https://github.com/boostorg/container_hash/issues/36#issuecomment-1978438308  

That's a pretty interesting issue.  
  
The reason for the error is that `std::iterator_traits` causes a hard error when instantiated with `const void*`, and `boost::hash` uses `std::iterator_traits` with the return type of `begin()` and `end()` to detect whether the type is a container and enable its built-in support for containers.  
  
Looks like `std::iterator_traits` has been fixed in C++20, which is why it works there.  
  
I'll look into getting this fixed for the upcoming 1.85. In the meantime, you can make this work by overriding `boost::container_hash::is_range<foo::bar>` to report false:  
  
```  
template<> struct boost::container_hash::is_range<foo::bar>: std::false_type {};  
```  
  
as in https://godbolt.org/z/fc8h7KjsM.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-03-06 08:31:36 UTC  
> Url: https://github.com/boostorg/container_hash/issues/36#issuecomment-1980332782  

Should be fixed for 1.85. The beta will be going out in a few days.

---

## Comment 3

> Username: martinsjogren  
> Created at: 2024-03-06 09:52:55 UTC  
> Url: https://github.com/boostorg/container_hash/issues/36#issuecomment-1980484816  

Super, and thank you for the workaround!
