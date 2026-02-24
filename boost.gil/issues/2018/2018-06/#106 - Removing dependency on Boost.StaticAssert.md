# #106 - Removing dependency on Boost.StaticAssert [Closed]

> Username: mloskot  
> Created at: 2018-06-24 22:40:27 UTC  
> Updated at: 2019-01-11 09:33:10 UTC  
> Closed at: 2019-01-11 09:33:10 UTC  
> Url: https://github.com/boostorg/gil/issues/106  

## Problem  
  
Currently, we use [BOOST_STATIC_ASSERT](https://www.boost.org/doc/libs/1_67_0/doc/html/boost_staticassert.html). In C++11, there is `static_assert`.  
  
Obvious choice to replace `BOOST_STATIC_ASSERT` with `static_assert`, but this will require tedious or artificial generation of assertion messages, since the `static_assert(exp)` variant is available since C++17. The `BOOST_STATIC_ASSERT(x)` has one nice feature - error message is a stringized version of `x`.  
  
## Solutions  
  
1. Switch to to [static_assert](https://en.cppreference.com/w/cpp/language/static_assert) manually adding error messages  
2. Define `BOOST_GIL_STATIC_ASSERT` in the same way as `BOOST_STATIC_ASSERT` with implicit message.  
3. Do nothing and keep `#include <boost/static_assert.hpp>`.  
  
## Question  
  
What shall we prefer?  
  
/cc @chhenning, @stefanseefeld

---

## Comment 1

> Username: mloskot  
> Created at: 2019-01-10 22:51:20 UTC  
> Url: https://github.com/boostorg/gil/issues/106#issuecomment-453286948  

FYI, PR #207 implements option 1.
