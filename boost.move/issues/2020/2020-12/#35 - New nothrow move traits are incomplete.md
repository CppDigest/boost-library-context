# #35 - New nothrow move traits are incomplete [Closed]

> Username: palebedev  
> Created at: 2020-12-15 22:30:13 UTC  
> Updated at: 2020-12-29 23:10:09 UTC  
> Closed at: 2020-12-29 23:08:27 UTC  
> Url: https://github.com/boostorg/move/issues/35  

The following fails to compile with boost 1.75, and recent clang+libc++ in C++17+:  
  
```c++  
#include <boost/container/small_vector.hpp>  
  
#include <type_traits>  
  
struct S  
{  
    // All non-trivial, but noexcept  
    S() noexcept {}  
    S(const S&) noexcept {}  
    S(S&&) noexcept {}  
    S& operator=(const S&) noexcept { return *this; }  
    S& operator=(S&&) noexcept { return *this; }  
};  
  
static_assert(std::is_nothrow_move_constructible_v<boost::container::small_vector<S,2>>);  
```  
  
This fails due to `small_vector`'s move constructor noexcept specification being `boost::container::dtl::is_nothrow_move_assignable<value_type>::value` which defers to `BOOST_MOVE_IS_NOTHROW_MOVE_ASSIGNABLE(T)`.  
  
It appears that a recent update in 9a77e69b only added definitions of `BOOST_MOVE_HAS_NOTHROW_MOVE_{ASSIGN,CONSTRUCT}` for MSVC++, but not for other compilers including clang, which causes them to use a simplified and imprecise emulation based on triviality.  
  
After adding  
```c++  
#     define BOOST_MOVE_HAS_NOTHROW_MOVE_ASSIGN(T) (__is_assignable(T, T &&) && __is_nothrow_assignable(T, T &&))  
```  
after `BOOST_MOVE_HAS_NOTHROW_ASSIGN` and  
```c++  
#     define BOOST_MOVE_HAS_NOTHROW_MOVE_CONSTRUCT(T) (__is_constructible(T, T &&) && __is_nothrow_constructible(T, T &&))  
```  
after `BOOST_MOVE_HAS_NOTHROW_COPY` it still doesn't work.  
  
Turns out, the way `BOOST_MOVE_HAS_TRAIT` is defined for Clang is wrong. As stated in https://clang.llvm.org/docs/LanguageExtensions.html#type-trait-primitives `__has_feature` (which is what `__has_extension` essentially is) only detects a listed subset of traits, not including `is_assignable` and others which Boost.Move tests. A universal test exists for Clang 6+ via `!__is_identifier(__trait)`, but since `__is_identifier` itself appeared earlier and AppleClang presumably has a different versioning scheme, I opted to replace  
```c++  
#     define BOOST_MOVE_HAS_TRAIT(T) __has_extension(T)  
```  
with  
```c++  
#     ifdef __is_identifier  
#       define BOOST_MOVE_HAS_TRAIT(T) (__has_extension(T) || !__is_identifier(__##T))  
#     else  
#       define BOOST_MOVE_HAS_TRAIT(T) __has_extension(T)  
#     endif  
```  
and this finally fixed this for me.  
  
This fixes it only for clang, other compilers would need something similar too.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-12-29 23:10:09 UTC  
> Url: https://github.com/boostorg/move/issues/35#issuecomment-752271619  

Many thanks for the issue. I've added intrinsics for both clang and GCC.
