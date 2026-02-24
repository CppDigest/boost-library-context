# #431 Add BOOST_NULLPTR helper macro [Merged]

> Username: pdimov  
> Created at: 2022-05-22 23:34:50 UTC  
> Updated at: 2022-06-13 15:47:28 UTC  
> Merged at: 2022-06-05 11:50:15 UTC  
> Closed at: 2022-06-05 11:50:15 UTC  
> Url: https://github.com/boostorg/config/pull/431  

Compilers and static analysis tools have started warning on the use of integral null pointer constants, suggesting that they need to be replaced with `nullptr`. This leads to people submitting pull requests that replace the uses of `0` with `nullptr`, such as for example https://github.com/boostorg/throw_exception/pull/22 (https://github.com/boostorg/throw_exception/pull/22/commits/8678b7cb09ad71bffbc24b099fc2a86bd925046a). However, `nullptr` is not available in C++03, which necessitates checking `BOOST_NO_CXX11_NULLPTR` as in https://github.com/boostorg/throw_exception/pull/22/commits/0a752cc9f87be1faaf8d7adcbc2cf4220623fbc1.  
  
The proposed helper macro `BOOST_NULLPTR` expands to `nullptr` when it's supported, `0` otherwise, which avoids the need to check `BOOST_NO_CXX11_NULLPTR` in libraries themselves.  
  
It would be more correct to expand this to `NULL` when `nullptr` isn't available, as `NULL` has special meaning for GCC and doesn't cause the warning, even in C++03 mode. But this requires that `<stddef.h>` be included in `suffix.hpp`, and I wasn't sure that this was acceptable.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-05-28 17:39:00 UTC  
> Url: https://github.com/boostorg/config/pull/431#issuecomment-1140303168  

Well?

---

## Comment 2

> Username: glenfe  
> Created_at: 2022-05-28 20:02:47 UTC  
> Url: https://github.com/boostorg/config/pull/431#issuecomment-1140322450  

Looks good to me, let's see what John thinks.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-05-29 08:17:08 UTC  
> Url: https://github.com/boostorg/config/pull/431#issuecomment-1140401059  

Sorry to be late to this... busy with tax returns :(  
  
I'm fine with <cstddef> being included if it's done only when needed ie in C++03.  
  
The failing drone CI runs look like their no-longer supported configurations.

---

## Comment 4

> Username: glenfe  
> Created_at: 2022-05-29 19:41:47 UTC  
> Url: https://github.com/boostorg/config/pull/431#issuecomment-1140512207  

Yes, only those Boost libraries that support C++03 would use BOOST_NULLPTR, the rest would just use nullptr.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2022-06-05 18:21:26 UTC  
> Url: https://github.com/boostorg/config/pull/431#issuecomment-1146860544  

> It would be more correct to expand this to NULL when nullptr isn't available, as NULL has special meaning for GCC and doesn't cause the warning, even in C++03 mode. But this requires that <stddef.h> be included in suffix.hpp, and I wasn't sure that this was acceptable.  
  
I would have done that and conditionally include it (for C++03). IMO it is better to be (more) correct here and that header is likely one of the lightest and most likely will be included anyway at least transitively.

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-06-13 11:44:52 UTC  
> Url: https://github.com/boostorg/config/pull/431#issuecomment-1153814337  

Would be nice to have this merged to master as well.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2022-06-13 15:47:28 UTC  
> Url: https://github.com/boostorg/config/pull/431#issuecomment-1154087976  

Done for both.

---
