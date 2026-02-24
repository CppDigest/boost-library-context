# #1348 - Fix for buffer overflow in fp_traits.hpp on aarch64 / mingw [Open]

> Username: joostn  
> Created at: 2026-01-10 12:37:29 UTC  
> Updated at: 2026-01-24 21:02:05 UTC  
> Url: https://github.com/boostorg/math/issues/1348  

Hi,  
  
I'm targeting aarch64-w64-mingw32 (probably I'm the first to try) and get this error:  
  
```  
/home/joostn/devel/wxwprojects_ptgui13/build/windows_mingw/arm64/RelWithDebInfo/thirdparty/boost-boost-1.90.0-prefix/include/boost/math/special_functions/detail/fp_traits.hpp:452:9: error: 'memcpy' will always overflow; destination buffer has size 0, but size argument is 4 [-Werror,-Wfortify-source]  
  452 |         std::memcpy(reinterpret_cast<unsigned char*>(&x) + offset_, &a, 4);  
      |         ^  
1 error generated.  
```  
  
In include/boost/math/special_functions/detail/fp_traits.hpp you have this:  
  
```  
#if defined(BOOST_NO_INT64_T) || defined(BOOST_NO_INCLASS_MEMBER_INITIALIZATION)\  
   || defined(BOOST_BORLANDC) || defined(__CODEGEAR__) || (defined(__APPLE__) && defined(__aarch64__)) || defined(_MSC_VER)  
```  
  
but AFAIK aarch64-w64-mingw32 also has 64 bit long double, so it should also take that path. This would fix it:  
  
```#if defined(BOOST_NO_INT64_T) || defined(BOOST_NO_INCLASS_MEMBER_INITIALIZATION)\  
   || defined(BOOST_BORLANDC) || defined(__CODEGEAR__) || (defined(__APPLE__) && defined(__aarch64__)) || defined(_MSC_VER) || (defined(__MINGW32__) && defined(_WIN32))  
```  
  
I'm not sure if there are other places where this should be fixed, or if there are side effects, so I don't dare to submit a pull request.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2026-01-10 19:19:39 UTC  
> Url: https://github.com/boostorg/math/issues/1348#issuecomment-3733407915  

Hi @joostn, thanks for this good find.  
  
We can handle this for sure. But we should discuss a few details in order to find the best way to handle this issue.  
  
It looks like you are getting an error via `-Werror` regarding a suspected buffer overflow. Some similar warnings we encountered in the past have proven to be false negative, bogus warnings.  
  
So we need to see if the warning can safely be disabled for this compiler and code sequence. if so, then we simply disable the warning within a PP-block in the header itself.  
  
So just to be sure, without `-Werror`, is this _just_ a warning?  
Then we can look at the warning and try to determin if it is real/bogus.  
Then potentially disable.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2026-01-10 19:21:33 UTC  
> Updated at: 2026-01-10 19:22:51 UTC  
> Url: https://github.com/boostorg/math/issues/1348#issuecomment-3733409538  

And yes, the hard-coded number `4` does look like it _might_ simply be wrong for this architecture. We need to catch that if this is the case. Then that would be a real warning/error and not a bogus one.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2026-01-11 12:51:17 UTC  
> Url: https://github.com/boostorg/math/issues/1348#issuecomment-3734520387  

> And yes, the hard-coded number 4 does look like it might simply be wrong for this architecture. We need to catch that if this is the case. Then that would be a real warning/error and not a bogus one.  
  
It's based on the assumption that the final #else is always a 128-bit long double, but this DR proves that not to be true, so maybe we should add a bit more error checking... I'll file a PR.

---

## Comment 4

> Username: joostn  
> Created at: 2026-01-11 14:14:09 UTC  
> Url: https://github.com/boostorg/math/issues/1348#issuecomment-3734619731  

I tested it for the 5 clang toolchains I have at hand:  
  
```C++  
#if defined(__APPLE__)  
  #if defined(__aarch64__)  
    static_assert(sizeof(long double) == 8);  
  #elif defined(__x86_64__)  
    static_assert(sizeof(long double) == 16);  
  #else  
    #error "Unsupported architecture"  
  #endif  
#elif (defined(_WIN32) && defined(_MSC_VER) && defined __clang__)  
  #if defined(__aarch64__)  
    static_assert(sizeof(long double) == 8);  
  #elif defined(__x86_64__)  
    static_assert(sizeof(long double) == 8);  
  #else  
    #error "Unsupported architecture"  
  #endif  
#elif (defined(_WIN32) && defined(__MINGW32__) && defined __clang__)  
  #if defined(__aarch64__)  
    static_assert(sizeof(long double) == 8);  
  #elif defined(__x86_64__)  
    static_assert(sizeof(long double) == 16);  
  #else  
    #error "Unsupported architecture"  
  #endif  
#elif (defined(__linux__) && defined __clang__)  
  #if defined(__x86_64__)  
    static_assert(sizeof(long double) == 16);  
  #else  
    #error "Unsupported architecture"  
  #endif  
#else  
  #error "untested"  
#endif  
```  
  
I think it would be good to add static assertions to fp_traits.hpp, mismatches would be caught at compile time

---

## Comment 5

> Username: ckormanyos  
> Created at: 2026-01-24 19:28:43 UTC  
> Updated at: 2026-01-24 21:02:05 UTC  
> Url: https://github.com/boostorg/math/issues/1348#issuecomment-3795388570  

I'd like to support embedded `arm-none-eabi` also, even if this is not an _OFFICIALLY_ supported compiler, it's nice to have in Math.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2026-01-24 19:34:51 UTC  
> Url: https://github.com/boostorg/math/issues/1348#issuecomment-3795395325  

See also #1352
