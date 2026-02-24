# #140 Fix compilation with gcc 4.6 and older [Closed]

> Username: Lastique  
> Created at: 2018-04-22 21:42:11 UTC  
> Updated at: 2018-06-20 14:17:51 UTC  
> Closed at: 2018-06-20 13:33:22 UTC  
> Url: https://github.com/boostorg/test/pull/140  

The compiler does not support the "using" syntax for typedefs, even non-template, until gcc 4.7.  
  
This should fix these test failures:  
  
https://www.boost.org/development/tests/develop/developer/output/igaztanaga-develop-gcc-4-6c++11-boost-bin-v2-libs-log-test-attr_attribute_set-test-gcc-4-6c+-dbg-dbg-symbl-off-thrdp-wn32-thrd-mlt.html  
https://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-4-warn-Docker-64on64-boost-bin-v2-libs-log-test-attr_attribute_set-test-gcc-4-4~c++0x~warn-debug-threadapi-pthread-threading-multi.html  
  
Untested.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-04-22 22:21:38 UTC  
> Url: https://github.com/boostorg/test/pull/140#issuecomment-383416964  

Isn't there any `BOOST_NO_CXX11_USING_XXX` that may prevent the compilation of this altogether ? I failed to find the proper one, but this part of the code is already in a preprocessor conditional.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-04-22 23:26:19 UTC  
> Url: https://github.com/boostorg/test/pull/140#issuecomment-383420552  

There is `BOOST_NO_CXX11_TEMPLATE_ALIASES`. But I figured the `typedef`s are a portable replacement that did not require disabling the code. Granted, I don't know what the code does...

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2018-04-27 16:10:40 UTC  
> Url: https://github.com/boostorg/test/pull/140#issuecomment-385018299  

Does the branch `topic/13525-compilation-error-GCC-4-6-3` work for you?

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-04-27 18:39:04 UTC  
> Url: https://github.com/boostorg/test/pull/140#issuecomment-385058330  

I don't have a way to test it. The failures were detected in the official Boost testing.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2018-06-20 13:33:22 UTC  
> Updated_at: 2018-06-20 13:34:07 UTC  
> Url: https://github.com/boostorg/test/pull/140#issuecomment-398749731  

Fixed on develop (via other changes), thanks. See Trac 13525

---
