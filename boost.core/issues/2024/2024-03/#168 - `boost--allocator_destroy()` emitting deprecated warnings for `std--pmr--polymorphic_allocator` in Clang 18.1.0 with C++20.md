# #168 - `boost::allocator_destroy()` emitting deprecated warnings for `std::pmr::polymorphic_allocator` in Clang 18.1.0 with C++20 [Closed]

> Username: k3DW  
> Created at: 2024-03-11 02:40:15 UTC  
> Updated at: 2024-03-19 15:10:05 UTC  
> Closed at: 2024-03-19 15:10:05 UTC  
> Url: https://github.com/boostorg/core/issues/168  

```  
#include <boost/core/allocator_access.hpp>  
#include <memory_resource>  
  
int main()  
{  
    std::pmr::polymorphic_allocator<int> alloc;  
  
    int* p = boost::allocator_allocate(alloc, 1);  
    boost::allocator_destroy(alloc, p);  
}  
```  
Compiled in `x86-64 clang 18.1.0` with `-std=c++20 -Werror`  
  
https://godbolt.org/z/seqWTr8zj  
  
```  
/opt/compiler-explorer/libs/boost_1_84_0/boost/core/allocator_access.hpp:567:7: error: 'destroy<int>' is deprecated: use 'allocator_traits::destroy' instead [-Werror,-Wdeprecated-declarations]  
  567 |     a.destroy(p);  
      |       ^  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-03-11 04:07:21 UTC  
> Url: https://github.com/boostorg/core/issues/168#issuecomment-1987599796  

I wonder why it only warns under Clang 18 using libstdc++ 13.2.0, but not under GCC 13.2.0 itself, or under Clang 17 (which also uses libstdc++ 13).

---

## Comment 2

> Username: k3DW  
> Created at: 2024-03-16 00:20:01 UTC  
> Url: https://github.com/boostorg/core/issues/168#issuecomment-2000754891  

The "allocator_access.hpp" header already has this and its corresponding "pop".  
```cpp  
#if defined(_MSC_VER)  
#pragma warning(push)  
#pragma warning(disable:4996)  
#endif  
```  
I suggest changing it to this, which looks similar to other places in the library.  
```cpp  
#if defined(__clang__) && defined(__has_warning)  
# if __has_warning("-Wdeprecated-declarations")  
#  pragma clang diagnostic push  
#  pragma clang diagnostic ignored "-Wdeprecated-declarations"  
# endif  
#elif defined(_MSC_VER)  
# pragma warning(push)  
# pragma warning(disable: 4996)  
#elif defined(__GNUC__) && !(defined(__INTEL_COMPILER) || defined(__ICL) || defined(__ICC) || defined(__ECC)) && (__GNUC__ * 100 + __GNUC_MINOR__) >= 406  
# pragma GCC diagnostic push  
# pragma GCC diagnostic ignored "-Wdeprecated-declarations"  
#endif  
```  
[Link to my suggested changes](https://github.com/boostorg/core/commit/0b18eeaba47ca20a9b615f32b525bd6e5f29875f)
