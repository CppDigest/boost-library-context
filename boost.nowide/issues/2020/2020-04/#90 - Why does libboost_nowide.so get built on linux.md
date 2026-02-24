# #90 - Why does libboost_nowide.so get built on linux? [Closed]

> Username: jwakely  
> Created at: 2020-04-24 23:23:14 UTC  
> Updated at: 2020-04-25 18:16:24 UTC  
> Closed at: 2020-04-25 18:16:24 UTC  
> Url: https://github.com/boostorg/nowide/issues/90  

If the library is header-only on non-Windows platforms, why is an unnecessary shared library built and installed?

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-04-25 13:19:07 UTC  
> Url: https://github.com/boostorg/nowide/issues/90#issuecomment-619378633  

The library is not header-only on non-Windows, there are some functions in the shared/static library.  
  
This is to simplify some stuff and e.g. make it possible to use the functions independent of the other compile options used. For example the environment related functions are not available with `-std=c++xx` as they are non-standard extensions. Using the library allows to use them in any case  
  
It is also consistent: No matter the target OS you build and link against the library.

---

## Comment 2

> Username: jwakely  
> Created at: 2020-04-25 15:35:04 UTC  
> Url: https://github.com/boostorg/nowide/issues/90#issuecomment-619397211  

So when packing Boost for Fedora Linux I should install the shared library? The existing `boost-nowide` package in Fedora doesn't do that.

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-04-25 17:16:32 UTC  
> Url: https://github.com/boostorg/nowide/issues/90#issuecomment-619412182  

That is correct. The pre-boost version was header only on Linux. However extensive testing revealed shortcomings especially as the -std=c++xx flag is now more widely used.  
So this was changed in favor of uniform usage.   
  
Btw as this is in boost 1.73 now you should consider packaging it like any other boost library. However the non-bundled version (build with cmake, requiring any boost version) may receive more frequent (tagged) updates at the cost of potential conflicts when mixing those 2 as nanespace etc is the same.   
  
Let me know if you run into any issues

---

## Comment 4

> Username: jwakely  
> Created at: 2020-04-25 18:16:24 UTC  
> Url: https://github.com/boostorg/nowide/issues/90#issuecomment-619420206  

Aha, I think the current Fedora package was based on the non-bundled one. Fedora 33 should have Boost 1.73.0 and so will include a `boost-nowide` package with the shared lib. Thanks for setting me straight!
