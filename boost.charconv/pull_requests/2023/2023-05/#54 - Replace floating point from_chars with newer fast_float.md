# #54 Replace floating point from_chars with newer fast_float [Merged]

> Username: mborland  
> Created at: 2023-05-26 13:50:29 UTC  
> Updated at: 2023-06-01 08:39:30 UTC  
> Merged at: 2023-06-01 08:39:27 UTC  
> Closed at: 2023-06-01 08:39:27 UTC  
> Url: https://github.com/boostorg/charconv/pull/54  

See: https://github.com/fastfloat/fast_float  
As of version 5 it is licensed under BSL.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-05-31 09:43:44 UTC  
> Url: https://github.com/boostorg/charconv/pull/54#issuecomment-1569852958  

Benchmarks from `benchmarks/from_chars_floating.cpp`. Run on an M1 Mac with macOS 13.4 and GCC 13:  
```  
GNU C++ version 13.1.0  
GNU libstdc++ version 20230426  
  
                std::strtox<float>, scientific:   607 ms (s=-2.83944e+20)  
            std::from_chars<float>, scientific:   530 ms (s=-2.83944e+20)  
boost::charconv::from_chars<float>, scientific:   535 ms (s=-2.83944e+20)  
  
                std::strtox<double>, scientific:   778 ms (s=1.47618e+299)  
            std::from_chars<double>, scientific:   531 ms (s=1.47618e+299)  
boost::charconv::from_chars<double>, scientific:   529 ms (s=1.47618e+299)  
  
                std::strtox<float>, general:   597 ms (s=-2.83944e+20)  
            std::from_chars<float>, general:   513 ms (s=-2.83944e+20)  
boost::charconv::from_chars<float>, general:   519 ms (s=-2.83944e+20)  
  
                std::strtox<double>, general:   770 ms (s=1.47618e+299)  
            std::from_chars<double>, general:   528 ms (s=1.47618e+299)  
boost::charconv::from_chars<double>, general:   526 ms (s=1.47618e+299)  
  
                std::strtox<float>, uint64:   571 ms (s=1.01167e+19)  
            std::from_chars<float>, uint64:   703 ms (s=1.01167e+19)  
boost::charconv::from_chars<float>, uint64:   713 ms (s=1.01167e+19)  
  
                std::strtox<double>, uint64:   564 ms (s=1.01167e+19)  
            std::from_chars<double>, uint64:   702 ms (s=1.01167e+19)  
boost::charconv::from_chars<double>, uint64:   708 ms (s=1.01167e+19)  
```  
  
On develop:  
  
```  
GNU C++ version 13.1.0  
GNU libstdc++ version 20230426  
  
                std::strtox<float>, scientific:   607 ms (s=-2.83944e+20)  
            std::from_chars<float>, scientific:   528 ms (s=-2.83944e+20)  
boost::charconv::from_chars<float>, scientific:   763 ms (s=-2.83944e+20)  
  
                std::strtox<double>, scientific:   780 ms (s=1.47618e+299)  
            std::from_chars<double>, scientific:   527 ms (s=1.47618e+299)  
boost::charconv::from_chars<double>, scientific:  1024 ms (s=1.47618e+299)  
  
                std::strtox<float>, general:   594 ms (s=-2.83944e+20)  
            std::from_chars<float>, general:   512 ms (s=-2.83944e+20)  
boost::charconv::from_chars<float>, general:   745 ms (s=-2.83944e+20)  
  
                std::strtox<double>, general:   770 ms (s=1.47618e+299)  
            std::from_chars<double>, general:   529 ms (s=1.47618e+299)  
boost::charconv::from_chars<double>, general:  1024 ms (s=1.47618e+299)  
  
                std::strtox<float>, uint64:   585 ms (s=1.01167e+19)  
            std::from_chars<float>, uint64:   705 ms (s=1.01167e+19)  
boost::charconv::from_chars<float>, uint64:   793 ms (s=1.01167e+19)  
  
                std::strtox<double>, uint64:   566 ms (s=1.01167e+19)  
            std::from_chars<double>, uint64:   706 ms (s=1.01167e+19)  
boost::charconv::from_chars<double>, uint64:   782 ms (s=1.01167e+19)  
```

---

## Comment 2

> Username: mborland  
> Created_at: 2023-05-31 11:38:17 UTC  
> Url: https://github.com/boostorg/charconv/pull/54#issuecomment-1570027949  

@pdimov I think this is good for review. The fast_float implementation is generally untouched outside of changing some macros.

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2023-05-31 11:42:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/54#pullrequestreview-1452967839  

📁 include/boost/charconv/chars_format.hpp

```diff
  13 | // chars_format is a bitmask type (16.3.3.3.3)
  12 |- enum class chars_format : unsigned
  14 |+ enum chars_format : unsigned
```

> Username: pdimov  
> Created_at: 2023-05-31 11:42:47 UTC  
> Updated_at: 2023-05-31 11:42:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/54#discussion_r1211575170  

This change doesn't seem correct to me. `chars_format` is part of the interface and is specified to be `enum class`. Why is this needed?

> Username: mborland  
> Created_at: 2023-05-31 11:53:40 UTC  
> Url: https://github.com/boostorg/charconv/pull/54#discussion_r1211587694  

He uses bitwise operators in the control flow (e.g. https://github.com/cppalliance/charconv/pull/54/files#diff-257fe44b316853ce9ec340001186ef24affd04a1406ece4f47b98a0bdb77b95dR200). I tried implementing all of the bitwise operators and then casting to bool, but it was ugly and compilers complained about forcing to bool. The unused header above is cruft from that attempt.

> Username: pdimov  
> Created_at: 2023-05-31 12:05:55 UTC  
> Url: https://github.com/boostorg/charconv/pull/54#discussion_r1211606080  

We can't just change the interface to whatever is convenient to the current implementation. `fmt & chars_format::scientific` needs to be changed to `(unsigned)fmt & (unsigned)chars_format::scientific`.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-05-31 11:42:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/54#pullrequestreview-1452968157  

📁 include/boost/charconv/chars_format.hpp

```diff
   6 | #define BOOST_CHARCONV_CHARS_FORMAT_HPP
   7 | 
   8 |+ #include <boost/charconv/detail/config.hpp>
```

> Username: pdimov  
> Created_at: 2023-05-31 11:42:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/54#discussion_r1211575368  

Why is this include needed?


---
