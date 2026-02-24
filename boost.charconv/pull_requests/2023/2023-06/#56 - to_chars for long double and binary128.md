# #56 to_chars for long double and binary128 [Merged]

> Username: mborland  
> Created at: 2023-06-01 09:50:18 UTC  
> Updated at: 2023-06-22 08:34:56 UTC  
> Merged at: 2023-06-22 08:34:53 UTC  
> Closed at: 2023-06-22 08:34:53 UTC  
> Url: https://github.com/boostorg/charconv/pull/56  

Derivative of ryu_generic_128 methods.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-06-06 09:25:27 UTC  
> Updated_at: 2023-06-06 12:48:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1578280750  

@pdimov Do you have any idea why GCC 4.8 would have an issue with signaling NaN? The same issue occurred when I used the glibc issignaling macro. I didn't see anything in the 4.9 release notes that would explain the error.  
  
```  
====== BEGIN OUTPUT ======  
libs/charconv/test/to_chars_float.cpp(86): test 'buffer5 == "nan(snan)"' ('nan' == 'nan(snan)') failed in function 'void non_finite_values(boost::charconv::chars_format, int) [with T = long double]'  
libs/charconv/test/to_chars_float.cpp(92): test 'buffer6 == "-nan(snan)"' ('-nan(ind)' == '-nan(snan)') failed in function 'void non_finite_values(boost::charconv::chars_format, int) [with T = long double]'  
2 errors detected.  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
```

---

## Comment 2

> Username: mborland  
> Created_at: 2023-06-14 13:56:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1591263516  

```  
====== BEGIN OUTPUT ======  
GNU C++ version 14.0.0 20230612 (experimental)  
GNU libstdc++ version 20230612  
  
            std::snprintf<float>, scientific, 0:  4071 ms (s=1249494190)  
            std::to_chars<float>, scientific, 0:   547 ms (s=1222675070)  
boost::charconv::to_chars<float>, scientific, 0:   435 ms (s=1222675070)  
  
            std::snprintf<float>, scientific, 6:  3902 ms (s=1209648310)  
            std::to_chars<float>, scientific, 6:  1047 ms (s=1209648310)  
boost::charconv::to_chars<float>, scientific, 6:   458 ms (s=1209648310)  
  
            std::snprintf<float>, general, 0:  4065 ms (s=1215305890)  
            std::to_chars<float>, general, 0:   632 ms (s=1213059940)  
boost::charconv::to_chars<float>, general, 0:   480 ms (s=1212721020)  
  
            std::snprintf<float>, general, 6:  4051 ms (s=1177644030)  
            std::to_chars<float>, general, 6:  1187 ms (s=1177644030)  
boost::charconv::to_chars<float>, general, 6:   483 ms (s=1207425580)  
  
            std::snprintf<double>, scientific, 0:  7615 ms (s=1427249960)  
            std::to_chars<double>, scientific, 0:   499 ms (s=1458911385)  
boost::charconv::to_chars<double>, scientific, 0:   507 ms (s=1415002950)  
  
            std::snprintf<double>, scientific, 6:  5599 ms (s=1227341370)  
            std::to_chars<double>, scientific, 6:  1101 ms (s=1227341370)  
boost::charconv::to_chars<double>, scientific, 6:   488 ms (s=1227341370)  
  
            std::snprintf<double>, general, 0:  7400 ms (s=1402505130)  
            std::to_chars<double>, general, 0:   506 ms (s=1448519075)  
boost::charconv::to_chars<double>, general, 0:   531 ms (s=1412564790)  
  
            std::snprintf<double>, general, 6:  5379 ms (s=1203912950)  
            std::to_chars<double>, general, 6:  1156 ms (s=1203912950)  
boost::charconv::to_chars<double>, general, 6:   514 ms (s=1225124730)  
  
            std::to_chars<_Float128>, scientific, 0:  2062 ms (s=5039881560)  
boost::charconv::to_chars<_Float128>, scientific, 0:  1810 ms (s=1319968980)  
  
            std::to_chars<_Float128>, scientific, 6: 53017 ms (s=1249958800)  
boost::charconv::to_chars<_Float128>, scientific, 6:   319 ms (s=4779887670)  
  
            std::to_chars<_Float128>, general, 0:  2075 ms (s=419990130)  
boost::charconv::to_chars<_Float128>, general, 0:  1803 ms (s=1319968980)  
  
            std::to_chars<_Float128>, general, 6: 51577 ms (s=1227660290)  
boost::charconv::to_chars<_Float128>, general, 6:   328 ms (s=4779887670)  
```  
  
Updated benchmarks with this PR.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-06-19 16:30:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1597464962  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-06-20 07:45:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598282317  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-06-20 08:05:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598309461  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-06-20 08:25:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598338887  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-06-20 09:35:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598443362  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-06-20 10:40:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598532083  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-06-20 11:05:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598571098  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-06-20 11:15:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598584209  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-06-20 11:40:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598614690  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-06-20 12:50:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598709893  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-06-20 13:00:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598725392  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-06-20 13:30:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598796713  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-06-20 14:00:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598854577  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-06-20 14:25:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598898402  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-06-20 14:40:17 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598924451  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-06-20 15:15:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1598993196  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-06-20 15:25:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1599011677  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2023-06-20 15:31:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1599026976  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-06-20 15:37:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1599037251  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Review 22 [Commented]

> Username: mborland  
> Created_at: 2023-06-21 07:28:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/56#pullrequestreview-1489956001  

📁 test/CMakeLists.txt

```diff
  11 | add_compile_definitions(BOOST_CHARCONV_CMAKE_TESTING)
  12 |- boost_test_jamfile(FILE Jamfile LINK_LIBRARIES Boost::charconv Boost::core Boost::assert)
  12 |+ boost_test_jamfile(FILE Jamfile LINK_LIBRARIES Boost::charconv Boost::core Boost::assert COMPILE_DEFINITIONS BOOST_CHARCONV_CMAKE_TESTING=1)
```

> Username: mborland  
> Created_at: 2023-06-21 07:28:29 UTC  
> Updated_at: 2023-06-21 07:28:42 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#discussion_r1236502081  

@pdimov What is the correct way to pass a define to `boost_test_jamfile`? In config I added that if `BOOST_CHARCONV_CMAKE_TESTING` is defined to not use `<quadmath.h>`. The issue with `set(CMAKE_CXX_EXTENSIONS OFF)` not doing what it is supposed to was fixed in CMake 3.23 which I think is too new to require.

> Username: pdimov  
> Created_at: 2023-06-21 07:29:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#discussion_r1236503850  

COMPILE_DEFINITIONS is the right way.


---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2023-06-21 07:50:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1600355638  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2023-06-21 08:00:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1600372870  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2023-06-21 09:25:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1600499809  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2023-06-21 09:45:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1600528114  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2023-06-21 09:55:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1600541762  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2023-06-21 11:10:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1600641400  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 29

> Username: mborland  
> Created_at: 2023-06-21 11:32:25 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1600670129  

@pdimov I believe this is good for review when you have time.

---

## Review 30 [Commented]

> Username: pdimov  
> Created_at: 2023-06-21 14:46:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/56#pullrequestreview-1490968100  

📁 include/boost/charconv/detail/config.hpp

```diff
 163 | #endif
 164 | 
 165 |+ #if BOOST_MSVC
```

> Username: pdimov  
> Created_at: 2023-06-21 14:46:53 UTC  
> Updated_at: 2023-06-21 14:46:54 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#discussion_r1237133802  

This should be `defined(BOOST_MSVC)`.


---

## Review 31 [Commented]

> Username: pdimov  
> Created_at: 2023-06-21 14:47:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/56#pullrequestreview-1490969653  

📁 include/boost/charconv/detail/config.hpp

```diff
 172 |+ #  if __has_cpp_attribute(assume)
 173 |+ #    define BOOST_CHARCONV_ASSUME(expr) [[assume(expr)]]
 174 |+ #  endif
```

> Username: pdimov  
> Created_at: 2023-06-21 14:47:27 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#discussion_r1237134684  

If `__has_cpp_attribute(assume)` is 0, `BOOST_CHARCONV_ASSUME` remains undefined.


---

## Review 32 [Commented]

> Username: pdimov  
> Created_at: 2023-06-21 14:51:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/56#pullrequestreview-1490979721  

📁 src/to_chars.cpp

```diff
  75 |-             *buffer = char('0' + n);
  76 |-         }
  71 |+         *buffer = char('0' | n);
```

> Username: pdimov  
> Created_at: 2023-06-21 14:51:29 UTC  
> Updated_at: 2023-06-21 14:51:30 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#discussion_r1237140751  

`'0' | n` is not guaranteed to be valid under all encodings, but `'0' + n` is.  
  
'0'..'9' are guaranteed to be consecutive, but the low hex digit is not guaranteed to be 0.


---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2023-06-22 07:30:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/56#issuecomment-1602149875  

An automated preview of the documentation is available at [https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://56.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
