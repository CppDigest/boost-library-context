# #87 Reduce dependency on Boost.TypeTraits now that C++ >= 11 is required. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2025-09-05 17:00:52 UTC  
> Updated at: 2025-10-05 09:17:49 UTC  
> Merged at: 2025-10-05 09:14:47 UTC  
> Closed at: 2025-10-05 09:14:48 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87  

This allows to workaround the following error when using clang >= 21 on the following code:  
  
```  
#include <string>  
#include <iostream>  
#include "boost/lexical_cast.hpp"  
  
enum SomeEnum  
{  
    OneValue  
};  
  
int main()  
{  
    std::cout << boost::lexical_cast<std::string>(SomeEnum::OneValue) << std::endl;  
}  
```  
  
Results in:  
  
```  
/app/boost/include/boost/type_traits/is_signed.hpp:37:25: error: in-class initializer for static data member is not a constant expression  
   37 |    static const no_cv_t minus_one = (static_cast<no_cv_t>(-1));  
  
... (snapped)  
  
/app/boost/include/boost/type_traits/is_signed.hpp:37:38: note: integer value -1 is outside the valid range of values [0, 1] for the enumeration type 'SomeEnum'  
   37 |    static const no_cv_t minus_one = (static_cast<no_cv_t>(-1));  
   ```

---

## Review 1 [Commented]

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-05 22:02:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/lexical_cast/pull/87#pullrequestreview-3190890952  

📁 test/integral_types_test.cpp

```diff
  43 | // There's no typeinfo for unsigned __int128 in Xcode_15.0.1
  44 |- #if defined(BOOST_HAS_INT128) && !defined(BOOST_LEXICAL_CAST_TEST_NO_128_INTS) && !defined(__APPLE__)
  44 |+ #if defined(BOOST_HAS_INT128) && !defined(BOOST_LEXICAL_CAST_TEST_NO_128_INTS) && !defined(__APPLE__) && !defined(__STRICT_ANSI__)
```

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-05 21:58:13 UTC  
> Updated_at: 2025-09-05 22:02:53 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2326121584  

We have to disable these tests when using `-std=c++XX` as there is no GNU extension defining the 128 bits ints and things like `std::make_unsigned<__int28>` aren't correctly defined when GNU extensions are disabled. Tests are enabled when using `-std=gnu++XX` (which doesn't define the `__STRICT_ANSI__` macro).

> Username: pdimov  
> Created_at: 2025-09-05 22:53:08 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2326189694  

I'm not sure that this regression in functionality is acceptable.  
  
I've been defining my own make_unsigned and friends to get around this GCC issue. E.g. https://github.com/boostorg/container_hash/blob/060d4aea6b5b59d2c9146b7d8e994735b2c0a582/include/boost/container_hash/detail/hash_integral.hpp#L18 and below.

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-06 09:52:04 UTC  
> Updated_at: 2025-09-06 09:52:05 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2326741282  

Arg... yes, I naively thought no one would use the int128 extension when explicitly using the strict conformance mode, seems I was wrong.  
  
After reporting it to Jonathan in https://gcc.gnu.org/bugzilla/show_bug.cgi?id=121819 I was explained a bit why gcc did so that so far, and actually he tracked that issue already in https://gcc.gnu.org/bugzilla/show_bug.cgi?id=96710. The bad news is that indeed the following doesn't work with any gcc/libstdc++ <= 15 if using `-std=c++11`:  
  
```  
#include <type_traits>  
  
static_assert(std::is_integral<__int128_t>::value, "");  
static_assert(std::is_integral<__uint128_t>::value, "");  
  
static_assert(std::is_signed<__int128_t>::value, "");  
static_assert(std::is_unsigned<__uint128_t>::value, "");  
  
static_assert(std::is_same<std::make_signed<__uint128_t>::type, __int128_t>::value, "");  
static_assert(std::is_same<std::make_unsigned<__int128_t>::type, __uint128_t>::value, "");  
```  
  
but it now works fine, whatever `-std=XX` is used with any future gcc/libstdc++ >= 16.  
  
So I am not sure what we shall do here ? My initial issue is clearly with `boost::is_signed` and `boost::is_unsigned` which are now strong errors when used on small enums, and I can't use the std one because of this int128 issue... Shall we:  
 - push partially some of `boost` to `std` conversion that I am proposing here + consider in Boost.TypeTraits this fix for the enums: https://github.com/boostorg/type_traits/pull/199 ?  
  - Do some strange machinery internal to lexical cast to use boost type_traits for gcc <= 15 and somehow the std one for gcc >= 16 ? (I doubt so)  
   - Do nothing at all in lexical cast, and just consider the type_traits fix in https://github.com/boostorg/type_traits/pull/199 ?

> Username: pdimov  
> Created_at: 2025-09-06 10:15:02 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2326800636  

As I said, I just define these traits in a detail namespace, specialized so that they work for __int128. See the link above.


📁 include/boost/lexical_cast/detail/converter_lexical_streams.hpp

```diff
 304 |         template <class Type>
 315 |-         typename boost::enable_if_c<boost::is_signed<Type>::value && !boost::is_enum<Type>::value, bool>::type
 305 |+         typename std::enable_if<!std::is_floating_point<Type>::value && std::is_signed<Type>::value && !std::is_enum<Type>::value, bool>::type
```

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-05 22:02:45 UTC  
> Updated_at: 2025-09-05 22:02:53 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2326126674  

We have to add the `!std::is_floating_point<Type>::value` check here, since `boost::is_signed` and `std::is_signed` behave differently. We have this (which you can double check on Compiler Explorer here: https://godbolt.org/z/xjYWGrTce)  
  
```  
static_assert(std::is_signed<float>::value);  
static_assert(!boost::is_signed<float>::value);  
```  
  
(same for all kind of floating point types).  
  
If we don't change this, then the tests `is_optimized_stream` in `test/stream_traits_test.cpp` are failing for the float to string conversions in optimized mode.


---

## Review 2 [Commented]

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-12 13:26:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/lexical_cast/pull/87#pullrequestreview-3216720508  

📁 include/boost/lexical_cast/detail/type_traits.hpp

```diff
   1 |+ // Copyright Romain Geissler, 2025.
```

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-12 13:26:30 UTC  
> Updated_at: 2025-09-12 13:26:31 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2344264542  

@pdimov Since this is a copy/paste from your work, should I mention your name in the copyright instead ?

> Username: pdimov  
> Created_at: 2025-09-12 14:24:14 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2344423663  

It's better to keep your line and add a separate one for me.

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-12 14:27:57 UTC  
> Updated_at: 2025-09-12 14:27:58 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#discussion_r2344433234  

Alright, there are now 2 copyright lines, mentionning each of us.


---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-26 16:09:30 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#issuecomment-3339393744  

Ping

---

## Comment 4

> Username: apolukhin  
> Created_at: 2025-10-05 09:17:49 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/87#issuecomment-3368888743  

Many thanks for the PR! It should improve compile times for big modern projects that use LexicalCast. I was attempting to do the proposed change a few years ago, but the int128_t issues stopped me from doing so

---
