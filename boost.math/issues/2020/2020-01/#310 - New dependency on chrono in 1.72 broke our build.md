# #310 - New dependency on chrono in 1.72 broke our build. [Closed]

> Username: barcharcraz  
> Created at: 2020-01-24 00:26:53 UTC  
> Updated at: 2020-02-06 12:56:08 UTC  
> Closed at: 2020-02-06 12:56:08 UTC  
> Url: https://github.com/boostorg/math/issues/310  

See https://github.com/microsoft/STL/pull/428 for more details. Basically chrono has (optionally) a separately compiled component, and uses a prefix and suffix header to enforce abi, this breaks our build by triggering warnings.  
  
We can avoid this by defining the macro to use chrono header only, but we _really_ don't want to depend on boost chrono (at least not by accident)   
  
The commit in question for boost is f4e5abfbb1f2e3f1c07cb144a895620c8004e712, and it appears that it adds support for a wall-clock timeout on "hypergeometric_pFq".  
  
  
Is this intentional?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-01-24 06:34:19 UTC  
> Url: https://github.com/boostorg/math/issues/310#issuecomment-578009626  

> appears that it adds support for a wall-clock timeout on "hypergeometric_pFq"  
  
I had also noticed this, but remained silent earlier. I can investigate this phenomenon. it might seem unusual to need hard time in hypergeometric series calculations. Either it is intentional and needed or not.  
  
i will try to find out.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-01-24 08:52:56 UTC  
> Url: https://github.com/boostorg/math/issues/310#issuecomment-578044215  

It was intentional to have a timeout as otherwise pFq can just go on forever trying to reach the requested precision.  
  
However, this is new code, so I'm wondering how you're pulling it in?  Ah... I guess you're including `<boost/math/special_functions.hpp>` rather than the individual headers maybe?  
  
It does occur to me, that since use of chrono is an internal detail, and that header is C++11 anyway (initializer_lists), that we should just switch to `std::chrono`.

---

## Comment 3

> Username: barcharcraz  
> Created at: 2020-01-25 01:35:07 UTC  
> Url: https://github.com/boostorg/math/issues/310#issuecomment-578362988  

yep we were pulling in all of special_functions.hpp, I've switched our code to include just what we need, however it's still strange to pull in chrono. It looks like the hypergeometric series calculations already have support for number of iterations based timeout (as I would expect), however they _also_ support a wall clock timeout which is somewhat strange.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-01-28 15:17:33 UTC  
> Url: https://github.com/boostorg/math/issues/310#issuecomment-579294928  

>It looks like the hypergeometric series calculations already have support for number of iterations based timeout (as I would expect), however they also support a wall clock timeout which is somewhat strange.  
  
There are 2 different interfaces here for 2 different use cases: the regular fixed precision case uses an iteration limit as with all the other special functions, but there is also a variable precision interface which keeps upping the working precision until the required accuracy has been achieved - and that one uses a wall clock timeout (and is expected to take some considerable time to complete in some cases).  It could be debatable whether the latter interface belongs with this library or Multiprecision, and whether it's a header API or a tool, but it has proved to be exceptionally useful as a means of double checking other results, so I'm assuming others will find it useful also.

---

## Comment 5

> Username: barcharcraz  
> Created at: 2020-01-29 01:03:22 UTC  
> Url: https://github.com/boostorg/math/issues/310#issuecomment-579545940  

I don't quite understand why the variable precision interface needs wall clock timeout instead of just an iterator, or precision timeout, but I don't even know what hypergeometric_pFq is.  
  
We've landed our fixes and our build is no longer broken.  
  
Thanks for addressing this issue with the std::chrono change and with some explanation.
