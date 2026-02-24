# #50 - utf8_codecvt fails when UTF-16 input ends with surrogate [Closed]

> Username: Flamefire  
> Created at: 2020-01-08 12:57:33 UTC  
> Updated at: 2023-03-06 01:58:44 UTC  
> Closed at: 2023-03-06 01:58:44 UTC  
> Url: https://github.com/boostorg/locale/issues/50  

This is a bug I detected in Boost.Nowide which uses the old code from the generic_codecvt used here. But the issue remains.  
  
Situation:   
  
- Pass UTF-16 string that ends in (or consists only of) `U+D800` to `do_out`  
- Not end of string -> loop continued. https://github.com/boostorg/locale/blob/ccb8fbb9a1a0dbdffb1054ffa34e4aba1e425642/include/boost/locale/generic_codecvt.hpp#L355  
- Not in surrogate, so state is zero, so `ch = 0xD800` https://github.com/boostorg/locale/blob/ccb8fbb9a1a0dbdffb1054ffa34e4aba1e425642/include/boost/locale/generic_codecvt.hpp#L363  
- First surrogate detected -> `state = 0xD800`, from increased -> now equal to from_end: https://github.com/boostorg/locale/blob/ccb8fbb9a1a0dbdffb1054ffa34e4aba1e425642/include/boost/locale/generic_codecvt.hpp#L390  
- Loop is exited https://github.com/boostorg/locale/blob/ccb8fbb9a1a0dbdffb1054ffa34e4aba1e425642/include/boost/locale/generic_codecvt.hpp#L355  
- Result is still `ok` and `from==from_end` -> check fails https://github.com/boostorg/locale/blob/ccb8fbb9a1a0dbdffb1054ffa34e4aba1e425642/include/boost/locale/generic_codecvt.hpp#L421  
- `ok` is returned although `partial` should because "unexpected end of source buffer"  
  
Solution should be to also check the state.

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-01-08 15:30:58 UTC  
> Url: https://github.com/boostorg/locale/issues/50#issuecomment-572122073  

Intrestingly the C++11 codecvt from GCC behaves differently: It does not consume any input and returns `ok`: https://godbolt.org/z/nASHeL  
  
However https://en.cppreference.com/w/cpp/locale/codecvt/out mentions:  
  
> When performing N:M conversions, this function may return std::codecvt_base::partial after consuming all source characters (from_next == from_end). This means that another internal character is needed to complete the conversion (e.g. when converting UTF-16 to UTF-8, if the last character in the source buffer is a high surrogate).   
  
But this is a "may".

---

## Comment 2

> Username: dimztimz  
> Created at: 2020-10-04 11:18:25 UTC  
> Url: https://github.com/boostorg/locale/issues/50#issuecomment-703240342  

> Intrestingly the C++11 codecvt from GCC behaves differently: It does not consume any input and returns ok: https://godbolt.org/z/nASHeL  
  
The GCC codecvt's have some [active bugs](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=86419), don't take them as reference.  
  
Regarding this bug, if the UTF-16 string ends with leading surrogate, `partial` should be returned, and if it ends with unpaired trailing surrogate, `error` should be returned. You would be more helpful if you post code that reproduces the bug.

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-10-05 11:08:30 UTC  
> Url: https://github.com/boostorg/locale/issues/50#issuecomment-703562599  

From my test suite (slightly adapted):  
  
```  
            char buf[4] = {};  
            char* const to = buf;  
            char* const to_end = buf + 4;  
            char* to_next = to;  
            const char16_t* err_utf = u"\xD800"; // Trailing UTF-16 surrogate  
            std::mbstate_t mb = std::mbstate_t();  
            const char16_t* from = err_utf;  
            const char16_t* from_end = from + 1;  
            const char16_t* from_next = from;  
            cvt_type::result res = cvt.out(mb, from, from_end, from_next, to, to_end, to_next);  
```  
  
FWIW: The above OP contains a full explanation what happens where. Follow that to verify that indeed `partial` is not returned because the state is not checked.
