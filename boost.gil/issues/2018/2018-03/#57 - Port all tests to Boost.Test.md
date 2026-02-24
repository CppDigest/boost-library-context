# #57 - [task] Port all tests to Boost.Test [Closed]

> Username: mloskot  
> Created at: 2018-03-20 23:14:18 UTC  
> Updated at: 2018-10-23 17:14:21 UTC  
> Closed at: 2018-10-23 17:14:21 UTC  
> Url: https://github.com/boostorg/gil/issues/57  

Currently, there is mixture of simple C++ programs used as tests with test suites/cases based on the Boost testing facilities.  
  
I think the library should aim for unification of the tests to:  
  
- have simpler, manageable tests organisation  
- make it easier for contributors to add test cas  
- receive detailed and usable failure reports from CI builds that pin-points problem, not just boolean answer pass/fail.  
  
Timeline: some time after the new IO extension is released, near Boost.GIL 3 :

---

## Comment 1

> Username: chhenning  
> Created at: 2018-03-21 00:03:20 UTC  
> Url: https://github.com/boostorg/gil/issues/57#issuecomment-374798361  

boost.test or gtest?

---

## Comment 2

> Username: mloskot  
> Created at: 2018-03-21 08:59:32 UTC  
> Updated at: 2018-03-21 09:18:06 UTC  
> Url: https://github.com/boostorg/gil/issues/57#issuecomment-374870446  

The choice of test framework is usually driven by subjective preferences, that is understandable.  
  
From practical point, why to introduce yet another third-party library as a dependency?  
  
I think it's reasonable to stick with the 'native' Boost tools, especially there are at least two to choose from:  
1. Boost.Test (header-only or linked binary) - a heavy testing weaponry  
2. Boost.Core's header-only `lightweight_test.hpp`, see https://www.boost.org/libs/core/doc/html/core/lightweight_test.html  
  
I'm personally eager to go for the `lightweight_test.hpp`:  
- small and simple  
- offers all features we need  
- regular `main`-based program  
- tests are easier to debug

---

## Comment 3

> Username: mloskot  
> Created at: 2018-10-23 17:14:21 UTC  
> Url: https://github.com/boostorg/gil/issues/57#issuecomment-432337364  

As this is already happening, closing.  
  
For simple single `.cpp` file tests, we prefer the Boost.Core.LightweightTest :-)  
For more complex test suites with large number of test cases, especially if fixtures are used with, then we use Boost.Test.
