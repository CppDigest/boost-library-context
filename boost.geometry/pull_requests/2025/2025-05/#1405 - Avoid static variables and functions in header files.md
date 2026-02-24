# #1405 Avoid static variables and functions in header files. [Merged]

> Username: bangerth  
> Created at: 2025-05-09 03:25:45 UTC  
> Updated at: 2025-07-16 15:59:34 UTC  
> Merged at: 2025-05-15 17:00:51 UTC  
> Closed at: 2025-05-15 17:00:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405  

I am working on converting a large scientific library (https://www.dealii.org) that builds on BOOST to C++20 modules, see https://github.com/dealii/dealii/issues/18071. As part of this effort, I have to find ways to "wrap" BOOST into a module partition, something that will look like this file: https://github.com/bangerth/dealii/blob/cxx-modules/cxx20-modules/interface_module_partitions/boost.ccm   
  
Wrapping works by "exporting" certain BOOST names via `using` declarations, but the targets of these `using` declarations can only be names that have "external linkage". This excludes `static` variables and functions because they have "internal linkage". GCC 15 also requires that all names *referenced* by entities I want to export *also* have external linkage, and this turns up quite a large number of `static` variables and functions in BOOST header files.  
  
There are good reasons to avoid internal linkage variables and functions in header files beyond my desire to use C++20 modules. In particular, they should be avoided because they are replicated in each translation unit that `#include`s a header file; if the object had external linkage, the linker would unify these copies into one, reducing code size.  
  
This patch addresses a number of places where this BOOST sub-library uses names with internal linkage in header files, by removing the `static` keyword and, where applicable, marking variables as `inline`. (Variables marked as `const` or `constexpr` implicitly have internal linkage unless marked with `inline`.)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-09 15:37:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1405#pullrequestreview-2828821162  

📁 include/boost/geometry/core/access.hpp

```diff
  35 | /// Index of minimum corner of the box.
  36 |- int const min_corner = 0;
  36 |+ inline int const min_corner = 0;
```

> Username: barendgehrels  
> Created_at: 2025-05-09 15:37:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#discussion_r2081951947  

Can it be made `constexpr`? For me that looks better than an inline global const

> Username: bangerth  
> Created_at: 2025-05-09 15:53:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#discussion_r2081975466  

It could *also* be made `constexpr` if you want. `const` or `constexpr` are the same with regard to linkage. It's the `inline` that is necessary to convert it from internal to external linkage.

> Username: bangerth  
> Created_at: 2025-05-09 16:00:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#discussion_r2081985858  

I should note that the variables here are *implicitly* also `constexpr` because (i) they are of literal type, and (ii) you are providing an initializer. As a consequence, you *can* add `constexpr` to the declaration, but it does not actually change anything.


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2025-05-09 15:40:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2867033498  

Thanks. I have no objections (apart from one question), it's just that there are much more functions that are marked as `static inline` and they are not handled. But we can do that later.

---

## Comment 3

> Username: bangerth  
> Created_at: 2025-05-09 16:00:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2867093116  

Yes, there are *hundreds* of such functions throughout BOOST :-) I'm an incrementalist in that I think that starting somewhere is better than waiting for a patch that fixes everything. So this is an increment :-)  
  
Would you like me to update the patch to use `constexpr` instead of `const`? Separately, `inline` variables are a C++17 thing. Does BOOST require C++17 these days?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-05-09 19:18:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2867669929  

> Separately, `inline` variables are a C++17 thing. Does BOOST require C++17 these days?  
  
It depends per library. Boost.Geometry is at C++14 so the `inline` should be removed for the variable. But you can make it `constexpr`, please do. Hope that will be sufficient for your use case.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2025-05-09 19:19:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2867671694  

> I'm an incrementalist in that I think that starting somewhere is better than waiting for a patch that fixes everything.  
  
I see, thanks. It just seemed a bit a random selection. I think we can remove all of them in one go (maybe with a few exceptions)

---

## Comment 6

> Username: bangerth  
> Created_at: 2025-05-09 21:01:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2867871998  

On 5/9/25 13:18, Barend Gehrels wrote:  
> Boost.Geometry is at C++14 so the |inline| should be removed for the variable.   
> But you can make it |constexpr|, please do. Hope that will be sufficient for   
> your use case.  
  
No, it's the 'inline' that's important for me. To export the variable in a   
C++20 module, I need it to have external linkage, which I can only get by   
using 'inline'. Is there a BOOST macro that makes something inline if we use   
C++17 or later, and expands to nothing for C++14 and earlier?

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2025-05-10 06:11:28 UTC  
> Updated_at: 2025-05-10 06:14:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2868459662  

> To export the variable in a C++20 module, I need it to have external linkage, which I can only get by using 'inline'. Is there a BOOST macro that makes something inline if we use C++17 or later, and expands to nothing for C++14 and earlier?  
  
I see, thanks.  
  
Yes, there is, you can use `BOOST_INLINE_CONSTEXPR` as done here, this is probably the most convenient because it does both things (`inline` and `constexpr`)  
  
* https://github.com/boostorg/mysql/blob/develop/include/boost/mysql/impl/internal/protocol/impl/binary_protocol.hpp#L55  
* https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/traits/std_integer_traits.hpp#L79  
  
Alternatively there is also `BOOST_INLINE_VARIABLE`

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2025-05-10 06:13:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2868462325  

It's included by `#include <boost/config.hpp>` (but it's implicitly included already by `boost/geometry/config.hpp`)

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2025-05-10 06:29:57 UTC  
> Updated_at: 2025-05-10 06:30:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2868500908  

About my remark  
> it's just that there are much more functions that are marked as static inline and they are not handled.  
  
I think you have most, or all, already in `Boost.Geometry`. Thanks!  
  
So after changing `inline` to `BOOST_INLINE_CONSTEXPR` as discussed we seem to be good to go and I'll approve.

---

## Review 10 [Commented]

> Username: tinko92  
> Created_at: 2025-05-10 06:40:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1405#pullrequestreview-2830439461  

Interesting, if this needs inline I'd certainly be in favour of the constexpr inline macro to make the constexpr explicit, even when it is implied.  
  
Is this change made redundant by DR2387 applied to C++14 and is there a compiler with sufficiently working module support to work for boost but no implementation of DR2387? If the answer is no to the first or yes to the second then I'm in favour of merging with use of the macro.

---

## Review 11 [Approved]

> Username: barendgehrels  
> Created_at: 2025-05-13 20:22:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1405#pullrequestreview-2838028296  

👍

---

## Comment 12

> Username: bangerth  
> Created_at: 2025-05-13 20:27:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2877862086  

I marked the two variables as `BOOST_INLINE_CONSTEXPR`. Let's see what your CI says.

---

## Comment 13

> Username: bangerth  
> Created_at: 2025-05-13 20:30:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2877869297  

@tinko92:  
  
> Is this change made redundant by DR2387 applied to C++14 and is there a compiler with sufficiently working module support to work for boost but no implementation of DR2387? If the answer is no to the first or yes to the second then I'm in favour of merging with use of the macro.  
  
DR2387 is unrelated. As for the compiler I'm using: Clang++-20 is sufficient for me to build modules for a project with 1000+ files and 1M+ lines of code. In fact, Clang-20 does not complain about the code I'm fixing here (with the exception of the two variables that need to be inline). It is GCC-15 that is more picky and complained about the places I'm pointing out here. GCC-15 has many other problems I am still working around.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2025-05-15 17:00:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2884500136  

@bangerth it's all green. I'll merge it right now. Thanks again for the contribution.

---

## Comment 15

> Username: bangerth  
> Created_at: 2025-05-15 18:45:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1405#issuecomment-2884762332  

Thank you, too! I will likely have more patches of this kind to various BOOST libraries.

---
