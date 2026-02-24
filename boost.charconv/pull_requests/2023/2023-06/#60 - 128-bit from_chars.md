# #60 128-bit from_chars [Merged]

> Username: mborland  
> Created at: 2023-06-22 08:36:08 UTC  
> Updated at: 2023-08-04 17:33:39 UTC  
> Merged at: 2023-08-04 17:33:35 UTC  
> Closed at: 2023-08-04 17:33:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/60  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-08-01 15:15:23 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1660534020  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-08-01 17:35:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1660793976  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-08-02 14:05:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1662277079  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 4

> Username: mborland  
> Created_at: 2023-08-02 15:21:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1662408129  

Updated benchmarks:  
  
```  
====== BEGIN OUTPUT ======  
GNU C++ version 13.1.0  
GNU libstdc++ version 20230512  
  
                std::strtox<float>, scientific:  1405 ms (s=-2.83944e+20)  
            std::from_chars<float>, scientific:   513 ms (s=-2.83944e+20)  
boost::charconv::from_chars<float>, scientific:   530 ms (s=-2.83944e+20)  
  
                std::strtox<double>, scientific:  2592 ms (s=1.47618e+299)  
            std::from_chars<double>, scientific:   538 ms (s=1.47618e+299)  
boost::charconv::from_chars<double>, scientific:   532 ms (s=1.47618e+299)  
  
                std::strtox<long double>, scientific:  7512 ms (s=inf)  
            std::from_chars<long double>, scientific:  7324 ms (s=inf)  
boost::charconv::from_chars<long double>, scientific:  7406 ms (s=inf)  
  
                std::strtox<float>, general:  1403 ms (s=-2.83944e+20)  
            std::from_chars<float>, general:   495 ms (s=-2.83944e+20)  
boost::charconv::from_chars<float>, general:   513 ms (s=-2.83944e+20)  
  
                std::strtox<double>, general:  2598 ms (s=1.47618e+299)  
            std::from_chars<double>, general:   541 ms (s=1.47618e+299)  
boost::charconv::from_chars<double>, general:   528 ms (s=1.47618e+299)  
  
                std::strtox<long double>, general:  7487 ms (s=inf)  
            std::from_chars<long double>, general:  7346 ms (s=inf)  
boost::charconv::from_chars<long double>, general:  7424 ms (s=inf)  
  
                std::strtox<float>, uint64:   871 ms (s=1.01167e+19)  
            std::from_chars<float>, uint64:   543 ms (s=1.01167e+19)  
boost::charconv::from_chars<float>, uint64:   562 ms (s=1.01167e+19)  
  
                std::strtox<double>, uint64:   890 ms (s=1.01167e+19)  
            std::from_chars<double>, uint64:   556 ms (s=1.01167e+19)  
boost::charconv::from_chars<double>, uint64:   558 ms (s=1.01167e+19)  
```

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-08-02 15:30:17 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1662423017  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-08-02 17:25:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1662656525  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-08-02 18:10:34 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1662716197  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 8

> Username: mborland  
> Created_at: 2023-08-03 15:11:52 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1664169239  

@pdimov Can you please take a look at this when you have a chance? The only failure is a package install; it has been happening sporadically the past few days.

---

## Comment 9

> Username: pdimov  
> Created_at: 2023-08-03 21:55:41 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1664693189  

That's a lot of a patch; from a cursory look, nothing particularly wrong jumps at me, except that testing a uniform distribution from 0.0L to LDBL_MAX is not going to test anything close to zero. We probably want one uniform distribution for the mantissa and another for the exponent.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-08-04 13:45:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1665631466  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-08-04 15:00:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1665750634  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-08-04 15:20:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1665777096  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-08-04 16:10:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/60#issuecomment-1665860207  

An automated preview of the documentation is available at [https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://60.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
