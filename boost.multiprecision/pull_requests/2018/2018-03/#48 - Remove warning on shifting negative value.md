# #48 Remove warning on shifting negative value. [Merged]

> Username: NAThompson  
> Created at: 2018-03-13 07:38:58 UTC  
> Updated at: 2018-05-12 01:09:03 UTC  
> Merged at: 2018-05-11 18:08:27 UTC  
> Closed at: 2018-05-11 18:08:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/48  

Using `b2 --toolset=clang --compiler=g++`, I received a pile of warnings of the form:  
  
```  
In file included from test_arithmetic_cpp_bin_float_3.cpp:8:  
../../../libs/multiprecision/test/test_arithmetic.hpp:371:56: warning: shifting a negative signed value is undefined [-Wshift-negative-value]  
      BOOST_CHECK_EQUAL(a << 10, (boost::intmax_t(-23) << 10));  
```  
  
This removes the test of shifted negative values, which might not be the correct action.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-03-13 08:10:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/48#issuecomment-372580625  

I think this is the wrong fix - I appreciate the tests invoke undefined behaviour (on the native integer types) - but we want this behaviour, and to test it in the multiprecision types.  Possibly the alternative is to test against integer literals instead?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-03-13 12:51:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/48#issuecomment-372654245  

It looks as if integer literals do indeed solve the problem. I just don't know what the literals should be! This is what I used:  
  
```  
      a = -1;  
      BOOST_CHECK_EQUAL(a << 10, 1024);  
      a = -23;  
      BOOST_CHECK_EQUAL(a << 10, 23552);  
      a = -23456;  
      BOOST_CHECK_EQUAL(a >> 10, 22);  
      a = -3;  
      BOOST_CHECK_EQUAL(a >> 10, -1);  
```

---
