# #157 - A challenge problem for constexpr multiprecision [Closed]

> Username: NAThompson  
> Created at: 2019-09-03 11:45:03 UTC  
> Updated at: 2019-11-25 18:10:00 UTC  
> Closed at: 2019-11-25 18:10:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/157  

The [first Feigenbaum constant](https://en.wikipedia.org/wiki/Feigenbaum_constants) seems totally unamenable to calculation via C++11-style constexpr, and its compile-time calculation to arbitrary precision so that it could be added to boost/math/constants/constants.hpp seems to require the semantics John is currently adding.  
  
A rapidly converging algorithm is given [here](https://www.ams.org/journals/mcom/1991-57-195/S0025-5718-1991-1079009-6/S0025-5718-1991-1079009-6.pdf) with "published" values to 1000 decimal places given [here](http://www.plouffe.fr/simon/constants/feigenbaum.txt).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-09-03 16:13:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/157#issuecomment-527529614  

Lol, that's quite a significant challenge (just implementing the algorithm!).  
  
The constexpr stuff also has a problem with compiler resources - for example my Hermite coefficient example is fine up to about order 12 or so, then basically drops off a cliff with the compiler just running the machine out of resources.  It's a recursive template meta-program though, so it seems to use exponential resources or something :(

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-09-03 17:05:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/157#issuecomment-527549591  

It actually does look pretty intimidating. I think I should've prefaced this with "only if you play around with it and find it fun!" because I doubt anyone will use an arbitrary precision Feigenbaum constant.
