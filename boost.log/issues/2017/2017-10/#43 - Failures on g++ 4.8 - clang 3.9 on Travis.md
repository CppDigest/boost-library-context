# #43 - Failures on g++ 4.8 / clang 3.9 on Travis [Closed]

> Username: pdimov  
> Created at: 2017-10-25 12:06:21 UTC  
> Updated at: 2017-10-29 19:31:34 UTC  
> Closed at: 2017-10-29 16:33:49 UTC  
> Url: https://github.com/boostorg/log/issues/43  

See https://travis-ci.org/boostorg/boost/builds/292271102  
  
```  
testing.capture-output bin.v2/libs/log/test/form_max_size_decor.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/threading-multi/form_max_size_decor.run  
====== BEGIN OUTPUT ======  
Running 3 test cases...  
Left: "При�"  
Right: "При"  
libs/log/test/run/form_max_size_decor.cpp(163): error: in "character_boundary_maintenance": check equal_strings(strm1.str(), strm2.str()) has failed  
Left: "П�>>>"  
Right: "П>>>"  
libs/log/test/run/form_max_size_decor.cpp(175): error: in "character_boundary_maintenance": check equal_strings(strm1.str(), strm2.str()) has failed  
*** 2 failures are detected in the test module "form_max_size_decor"  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
https://travis-ci.org/boostorg/boost/jobs/292271110#L2500  
https://travis-ci.org/boostorg/boost/jobs/292271111#L2615

---

## Comment 1

> Username: pdimov  
> Created at: 2017-10-27 15:31:42 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340005116  

Same on Appveyor.  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4213-develop

---

## Comment 2

> Username: Lastique  
> Created at: 2017-10-29 16:33:49 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340275324  

This is a bug in Boost.Detail introduced in https://github.com/boostorg/detail/commit/b47fccb4a4e7fb32b74d03f6f80250bc2cafe501 and fixed in https://github.com/boostorg/detail/commit/aee87734a8474f4ffd5047845a3dd9bbabc74418.

---

## Comment 3

> Username: pdimov  
> Created at: 2017-10-29 16:40:34 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340275758  

We should probably add a test for that.

---

## Comment 4

> Username: Lastique  
> Created at: 2017-10-29 16:42:42 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340275893  

C++11 `override` for those methods would be even better. We need `BOOST_OVERRIDE` in Boost.Config.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-29 17:03:26 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340277301  

Does the existing https://github.com/boostorg/detail/blob/develop/test/test_utf8_codecvt.cpp cover that?

---

## Comment 6

> Username: pdimov  
> Created at: 2017-10-29 17:08:12 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340277615  

`override` won't work, I think, because the point here is that we don't know which virtual we have, the standard or the non-standard one, so we have to override both.

---

## Comment 7

> Username: Lastique  
> Created at: 2017-10-29 18:12:09 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340282117  

I believe, the non-standard override is not the right way to do this. There should be just one override marked with `override` and its argument should be modified specifically for the compiler that requires that.

---

## Comment 8

> Username: Lastique  
> Created at: 2017-10-29 18:18:03 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340282591  

> Does the existing https://github.com/boostorg/detail/blob/develop/test/test_utf8_codecvt.cpp cover that?  
  
I can see it calls `length()` and test the result but apparently that didn't reveal the problem. I think that's because the default implementation of `do_length()` just does something similar to `strlen`, which returns the same result that the test expects. In other words, the default implementation does not detect UTF-8 character boundaries, which Boost.Log requires and tests for.

---

## Comment 9

> Username: pdimov  
> Created at: 2017-10-29 18:25:33 UTC  
> Updated at: 2017-10-29 18:28:34 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340283117  

> There should be just one override marked with  override  and its argument should be modified specifically for the compiler that requires that.  
  
The problem, if I recall, is that we either didn't know what compilers (actually standard libraries) require it, or that it wasn't possible to detect them reliably.  
  
I think that everything we tried ended up breaking somewhere, so in the end I suggested to just override both and be done with it.

---

## Comment 10

> Username: Lastique  
> Created at: 2017-10-29 18:33:01 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340283654  

IMHO, it's better to write the code as the standard requires and let everyone affected complain. Then add compiler checks on case-by-case basis.  
  
  
BTW, I've updated the codecvt test.

---

## Comment 11

> Username: pdimov  
> Created at: 2017-10-29 18:37:03 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340283934  

That's how it was. We had endless problems.

---

## Comment 12

> Username: Lastique  
> Created at: 2017-10-29 18:41:37 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340284264  

Boost.Locale has the same problem and defines `BOOST_LOCALE_DO_LENGTH_MBSTATE_CONST` for that.  
  
https://github.com/boostorg/locale/blob/develop/include/boost/locale/generic_codecvt.hpp#L28  
  
That doesn't look that scary at all. Of course, there may be other compilers not supported by Boost.Locale but really, how many are there?

---

## Comment 13

> Username: pdimov  
> Created at: 2017-10-29 18:50:16 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340284901  

At the moment, probably not that very many. In the past,  
  
https://github.com/boostorg/detail/commit/5367f6459d7ab55892c1ea67267ac0592c19686e#diff-211637c4197986a9bbdb9506133b746e  
  
from a quick sampling.  
  
But do whatever, I don't care. I don't use it, it won't break any of my code. :-)

---

## Comment 14

> Username: pdimov  
> Created at: 2017-10-29 18:51:46 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340284994  

More history: https://github.com/boostorg/detail/commit/eb6208f69ac85ceaa0bd13e050476d519d8e89fd#diff-211637c4197986a9bbdb9506133b746e

---

## Comment 15

> Username: Lastique  
> Created at: 2017-10-29 18:56:34 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340285333  

Thanks for the links. The second one basically amounts to what Boost.Locale does - a workaround for MSVC, one compiler. (The first one is backwards - it defaults to `const` and "works around" compilers that correctly don't have `const`; which is probably the reason why every compiler except MSVC appeared "broken".)

---

## Comment 16

> Username: pdimov  
> Created at: 2017-10-29 19:31:34 UTC  
> Url: https://github.com/boostorg/log/issues/43#issuecomment-340287707  

The problem is that the overload does have `const` in C++98, removed in C++03. The other problem was that Dinkumware was used much more widely in the past, it wasn't just MSVC. The third problem is that changing the overload silently breaks code (it still compiles but stops overriding), so I suspect standard libraries took their time in fixing it. Nowadays that's probably only of historical relevance. But it wasn't not as simple as "oh MSVC was broken, what else is news". The standard was broken.
