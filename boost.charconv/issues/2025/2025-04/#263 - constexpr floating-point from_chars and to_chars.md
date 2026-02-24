# #263 - constexpr floating-point from_chars and to_chars [Open]

> Username: leni536  
> Created at: 2025-04-08 13:20:10 UTC  
> Updated at: 2025-04-08 13:46:26 UTC  
> Url: https://github.com/boostorg/charconv/issues/263  

I am working on the C++ proposal [P3652](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3652r0.html) and I wonder if PRs for constexpr floating-point charconv functions would be well-received in this project. I contributed to the fast_float and dragonbox projects to make their implementations constexpr.

---

## Comment 1

> Username: mborland  
> Created at: 2025-04-08 13:32:00 UTC  
> Url: https://github.com/boostorg/charconv/issues/263#issuecomment-2786454563  

Yes, so long as they contain the proper compatibility macros to keep C++11 support in this library. I can review or answer questions, but you'll find a lot of it to be familiar since fast_float, dragonbox, and floff are the primary methods used internally. You'll also see Ryu which is used for `long double` and `__float128`. I know those can all be made constexpr pretty readily. The only thing that would not be easily constexpred is I rely on `<quadmath.h>` for some of `__float128`. The <cmath> type functions are easy to replace, but I also use `strtoflt128` which I believe uses Gay's method inside. I never felt like re-implementing it to break the dependency.

---

## Comment 2

> Username: leni536  
> Created at: 2025-04-08 13:46:25 UTC  
> Url: https://github.com/boostorg/charconv/issues/263#issuecomment-2786498321  

Regarding `strtoflt128`, I recently ported musl's strtod implementation to constexpr from_chars [here](https://github.com/leni536/musl_from_chars/). It supports 128 bit `long double`, so adding `__float128` would be fairly straightforward.
