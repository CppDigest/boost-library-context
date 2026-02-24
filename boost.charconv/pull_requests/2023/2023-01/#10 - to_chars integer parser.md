# #10 to_chars integer parser [Merged]

> Username: mborland  
> Created at: 2023-01-17 17:14:23 UTC  
> Updated at: 2023-02-06 18:06:45 UTC  
> Merged at: 2023-02-06 18:06:41 UTC  
> Closed at: 2023-02-06 18:06:41 UTC  
> Url: https://github.com/boostorg/charconv/pull/10  



---

## Comment 1

> Username: mborland  
> Created_at: 2023-01-18 20:21:43 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1387730079  

@pdimov Do you see anything obvious as to why Windows is failing in CI? It's all the same linker error.

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-01-19 00:22:47 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1396275787  

14.0 because of this:  
```  
D:\a\charconv\boost-root\boost/charconv/to_chars.hpp(107): error C2220: warning treated as error - no 'object' file generated  
D:\a\charconv\boost-root\boost/charconv/to_chars.hpp(107): warning C4127: conditional expression is constant  
```

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-01-19 00:26:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1396279127  

The rest seems to be because they try to link to the DLL, but it exports nothing, so no LIB is generated.  
  
I suppose now that the functions are constexpr, nothing remained in the DLL. But there will be one day, so maybe add a stub fp function just to have something exported.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-02-01 19:49:58 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1412633766  

@pdimov Can you take a look through this? I think it is in good shape.

---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2023-02-01 20:06:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/10#pullrequestreview-1279787979  

📁 test/Jamfile

```diff
  23 | run roundtrip.cpp ;
  24 | run from_chars_STL_comp.cpp : : : [ requires cxx17_std_apply ] ;
  25 |+ run to_chars_integer_STL_comp.cpp : : : [ requires cxx17_std_apply ] ;
```

> Username: pdimov  
> Created_at: 2023-02-01 20:06:24 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#discussion_r1093679132  

cxx17_std_apply doesn't seem the right condition here; why not cxx17_hdr_charconv?


---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2023-02-01 20:07:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/10#pullrequestreview-1279788743  

📁 include/boost/charconv/detail/integer_conversion.hpp

```diff
  20 |+ BOOST_CXX14_CONSTEXPR std::pair<std::uint32_t, std::uint32_t> unpack(std::uint64_t value)
  21 |+ {
  22 |+     auto x = static_cast<std::uint32_t>((value & UINT64_MAX) >> UINT64_C(32));
```

> Username: pdimov  
> Created_at: 2023-02-01 20:07:02 UTC  
> Updated_at: 2023-02-01 20:07:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#discussion_r1093679703  

`& UINT64_MAX` is a no-op here.


---

## Comment 7

> Username: pdimov  
> Created_at: 2023-02-01 20:10:46 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1412660248  

Looks good overall. The html should be in doc/html though, not doc, and shouldn't be checked in.  
  
I think we need significantly more test values than that; esp. against `<charconv>`, we need to be testing with both hand-picked values (1, 9, 10, 99, 100, 999, etc) and random values (e.g. from splitmix64.)  
  
(Like https://github.com/boostorg/core/blob/8977da6f50b3a29e429c9f78766296f66e2dab26/test/bit_ceil_test.cpp#L51-L62 for instance, except maybe with more than 1000 values.)

---

## Review 8 [Commented]

> Username: pdimov  
> Created_at: 2023-02-01 20:12:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/10#pullrequestreview-1279794885  

📁 include/boost/charconv/detail/is_constant_evaluated.hpp

```diff
  12 |+ #ifdef __has_include
  13 |+ # if __has_include(<version>)
  14 |+ #  include <version>
```

> Username: pdimov  
> Created_at: 2023-02-01 20:12:04 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#discussion_r1093683796  

We don't need `<version>` here. Just include `<type_traits>` and check the macro, without any of that has_include business.


---

## Review 9 [Commented]

> Username: mborland  
> Created_at: 2023-02-03 19:03:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/10#pullrequestreview-1283564726  

📁 include/boost/charconv/detail/memcpy.hpp

```diff
  21 |+ #  pragma GCC diagnostic ignored "-Wstringop-overflow"
  22 |+ #  define BOOST_CHARCONV_STRINGOP_OVERFLOW_DISABLED
  23 |+ #endif
```

> Username: mborland  
> Created_at: 2023-02-03 19:03:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#discussion_r1096171041  

@pdimov Any idea why GCC-11 would be still failing from "-Wstringop-overflow" with this? GCC-10 and 12 both had the same error from the same lines which were fixed ([old run here](https://github.com/CPPAlliance/charconv/actions/runs/4086466481)).

> Username: pdimov  
> Created_at: 2023-02-03 19:40:20 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#discussion_r1096206763  

I don't know, sorry.

> Username: mborland  
> Created_at: 2023-02-06 16:46:53 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#discussion_r1097643644  

Looks like it is related to this bug in GCC that was reported in 4.7.0 and will finally be fixed in 13 https://gcc.gnu.org/bugzilla/show_bug.cgi?id=53431


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-02-03 22:40:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1416491757  

An automated preview of the documentation is available at [https://10.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://10.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 11

> Username: sdarwin  
> Created_at: 2023-02-04 16:39:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1416796402  

https://master.charconv.cpp.al/  
https://develop.charconv.cpp.al/

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-02-06 16:50:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1419399451  

An automated preview of the documentation is available at [https://10.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://10.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-02-06 17:05:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1419422466  

An automated preview of the documentation is available at [https://10.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://10.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 14

> Username: mborland  
> Created_at: 2023-02-06 17:34:20 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1419468120  

@pdimov Can you please take a final look at this? I think I addressed all of your previous comments.

---

## Comment 15

> Username: pdimov  
> Created_at: 2023-02-06 17:53:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/10#issuecomment-1419506888  

Looks good to merge.

---
