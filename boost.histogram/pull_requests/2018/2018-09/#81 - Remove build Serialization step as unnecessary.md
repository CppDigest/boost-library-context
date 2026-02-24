# #81 Remove build Serialization step as unnecessary [Merged]

> Username: mloskot  
> Created at: 2018-09-15 22:11:29 UTC  
> Updated at: 2018-09-15 23:07:58 UTC  
> Merged at: 2018-09-15 22:39:31 UTC  
> Closed at: 2018-09-15 22:39:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/81  

Serialization will be built automatically as part of test which requires the library as a dependency.  
  
Add b2 command to build and run the speed tests.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2018-09-15 22:14:59 UTC  
> Url: https://github.com/boostorg/histogram/pull/81#issuecomment-421644008  

The speed tests are not intended to be part of the CI tests, but I think it is cool if they can be build with b2.  
However, you cannot run the speed tests without external libraries, therefore we should not list them in build.qbk or add a note that the speed tests require external libraries.

---

## Comment 2

> Username: coveralls  
> Created_at: 2018-09-15 22:16:38 UTC  
> Updated_at: 2018-09-15 22:39:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/81#issuecomment-421644221  

[![Coverage Status](https://coveralls.io/builds/19019314/badge)](https://coveralls.io/builds/19019314)  
  
Coverage remained the same at 99.499% when pulling **8953303534e477c8f6b244c48d7e96df02f2f6ff on mloskot:ml/doc-build-deps-cleanup** into **504654cb24ee5f51bca6b5d3945e0f085c3693bb on HDembinski:develop**.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-09-15 22:36:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/81#issuecomment-421646837  

I've removed the command from the docs. Sorry, I've only run the standalone `speed_cpp.cpp` test, missed the other require the third-parties.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2018-09-15 22:39:58 UTC  
> Updated_at: 2018-09-15 22:40:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/81#issuecomment-421647329  

Ok, merging! We could still just build the speed test `speed_cpp.cpp` which does not require external libs.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-09-15 23:07:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/81#issuecomment-421650934  

Good idea, done in #83

---
