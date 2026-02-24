# #598 Increase make_literal precision [Merged]

> Username: jagerman  
> Created at: 2016-04-24 21:38:43 UTC  
> Updated at: 2016-05-02 23:44:21 UTC  
> Merged at: 2016-04-25 18:59:13 UTC  
> Closed at: 2016-04-25 18:59:13 UTC  
> Url: https://github.com/boostorg/compute/pull/598  

boost::compute::detail::make_literal is losing some precision when making literal floating point values because it uses std::numeric_limits<T>::digits10, but that only gives us the number of decimal digits that will survive a decimal->native>decimal conversion; what we are doing is a native->decimal->native conversion, which can require up to ::max_digits10 (which is larger than ::digits10 by 2 (double) or 3 (float)).  
  
However, max_digits10 is a c++11 feature, so this commit uses digits10 + 3 when the c++11 numeric_limits isn't available.  That should be enough for an IEEE-style float, though is slightly more than necessary for some (e.g. double).  
  
I also added a test for this (which fails with the current conversion for both float and double, and passes with the proposed commits).

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-04-25 01:36:06 UTC  
> Url: https://github.com/boostorg/compute/pull/598#issuecomment-214086886  

[![Coverage Status](https://coveralls.io/builds/5913183/badge)](https://coveralls.io/builds/5913183)  
  
Coverage increased (+0.03%) to 88.82% when pulling **7f18293526049e28cd90c4067e83ad91429e1416 on jagerman:increase-literal-precision** into **48217d2918d64ac84c13822384c67c4edcad5481 on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-04-25 18:59:21 UTC  
> Url: https://github.com/boostorg/compute/pull/598#issuecomment-214480017  

Thanks for the fix!

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-04-30 17:44:00 UTC  
> Url: https://github.com/boostorg/compute/pull/598#issuecomment-215983177  

I've just noticed that on OSX it gives errors ([link](https://travis-ci.org/boostorg/compute/jobs/125438832#L2223)):  
  
``` bash  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:29: error: in "literal_conversion_float": check iss >> x has failed  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:30: error: in "literal_conversion_float": check char(iss.get()) == 'f' has failed [0xffffffff != 'f']  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:29: error: in "literal_conversion_float": check iss >> x has failed  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:30: error: in "literal_conversion_float": check char(iss.get()) == 'f' has failed [0xffffffff != 'f']  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:29: error: in "literal_conversion_float": check iss >> x has failed  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:30: error: in "literal_conversion_float": check char(iss.get()) == 'f' has failed [0xffffffff != 'f']  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:37: error: in "literal_conversion_float": check values[0] == roundtrip[0] has failed [1.23456788 != 0]  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:38: error: in "literal_conversion_float": check values[1] == roundtrip[1] has failed [1.234568 != 0]  
/Users/travis/build/boostorg/compute/test/test_literal_conversion.cpp:39: error: in "literal_conversion_float": check values[2] == roundtrip[2] has failed [1.23456812 != 0]  
```

---

## Comment 4

> Username: jagerman  
> Created_at: 2016-05-02 23:44:20 UTC  
> Url: https://github.com/boostorg/compute/pull/598#issuecomment-216397151  

I'm at a loss as why that is happening; my best guess is some OSX/clang++ bug w.r.t. std::istringstream operators and the stream status (I found at least one reference suggesting that, for some versions, peek() doesn't set eof as expected).  
  
I've rewritten the test code in PR #607 -- I don't know whether that will fix it (I don't have an easily accessible OS X machine), but if it fails, it should at least help figure out which part of the conversion is failing.

---
