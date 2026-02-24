# #264 - Boost Config check for std::variant [Closed]

> Username: sdebionne  
> Created at: 2019-03-12 21:05:52 UTC  
> Updated at: 2019-04-03 12:21:15 UTC  
> Closed at: 2019-04-02 17:47:07 UTC  
> Url: https://github.com/boostorg/config/issues/264  

Could you please add a check for the availability of C++17's std::variant?  
  
This would be useful in the context of PR boostorg/serialization#148 that adds support for `std::variant` to Boost.Serialization. The tests need to be disabled when the `variant` header is not available.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-03-26 11:19:19 UTC  
> Url: https://github.com/boostorg/config/issues/264#issuecomment-476582571  

I was going to say - use `__cpp_lib_variant` macro, but GCC 7, Clang 3.9, and MSVC 14.14 do have variant, but does not define `__cpp_lib_variant`.  
  
```cpp  
// if compiles, but returns 1 - the compiler does not set `__cpp_lib_variant`  
#ifdef __has_include  
# if __has_include(<version>)  
#  include <version>  
# endif  
#endif  
  
#include <variant>  
  
int main()  
{  
#ifdef __cpp_lib_variant  
    return 0;  
#else  
    return 1;  
#endif  
}  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-03-26 18:15:14 UTC  
> Url: https://github.com/boostorg/config/issues/264#issuecomment-476781715  

This is a mess, for gcc at least this works:  
  
```  
#ifdef __has_include  
#if __has_include(<variant>)  
#include <variant>  
  
#ifdef __cpp_lib_variant  
  
// Use variant only if we get here...  
  
#endif  
#endif  
#endif  
```  
  
However, this fails with msvc where including <variant> may be a hard error even though the __has_include succeeds.  
  
Given the proliferation of new headers in C++17 and 20 we need:  
  
* A general solution for all the new headers.  
* The solution absolutely must not involve #including half the std lib just to check the std config macros.  
* Easier to maintain than the current ad-hoc system.  
  
Let me think about this, but in the mean time suggestions (or PR's) are welcome!

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-26 18:19:14 UTC  
> Url: https://github.com/boostorg/config/issues/264#issuecomment-476783425  

As I said above `__cpp_lib_variant` is not defined on some compilers where variant is presented. Also `__has_include(<variant>)` will return `1` in C++14 mode.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-04-01 18:09:40 UTC  
> Url: https://github.com/boostorg/config/issues/264#issuecomment-478685166  

See https://github.com/boostorg/config/pull/271

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-04-02 17:47:07 UTC  
> Url: https://github.com/boostorg/config/issues/264#issuecomment-479116353  

Now in develop.

---

## Comment 6

> Username: sdebionne  
> Created at: 2019-04-03 08:31:46 UTC  
> Url: https://github.com/boostorg/config/issues/264#issuecomment-479392738  

Thanks John! I guess it is not going to make it in 1.70?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2019-04-03 12:21:15 UTC  
> Url: https://github.com/boostorg/config/issues/264#issuecomment-479465595  

No, I would say it's too late for new features now.
