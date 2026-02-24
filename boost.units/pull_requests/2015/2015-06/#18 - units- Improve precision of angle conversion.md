# #18 units: Improve precision of angle conversion [Open]

> Username: ghost  
> Created at: 2015-06-22 21:16:31 UTC  
> Updated at: 2023-04-11 15:03:30 UTC  
> Url: https://github.com/boostorg/units/pull/18  

Use high precision constants from boost/math/constants/constants.hpp  
to fix trac#6893.

---

## Comment 1

> Username: mkurdej  
> Created_at: 2015-06-23 07:57:45 UTC  
> Url: https://github.com/boostorg/units/pull/18#issuecomment-114396127  

Looks good to me, but I don't know what's the policy about dependencies on other Boost libraries. Anyone?  
Personally, I find it ok, as I would probably use math constants together with units anyway, so there will be no overhead actually.

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-06-24 05:02:32 UTC  
> Url: https://github.com/boostorg/units/pull/18#issuecomment-114728276  

We already depend on Boost.Math, see _units/cmath.hpp_ for the functions used there. But I'd like to have a unit test for this. My main concern is the question of hardcoding **double** precision instead of using whatever precision the user wants.

---

## Comment 3

> Username: ghost  
> Created_at: 2015-06-24 07:17:02 UTC  
> Url: https://github.com/boostorg/units/pull/18#issuecomment-114758121  

@jhunold But the double is hardcoded here:  
https://github.com/boostorg/units/blob/master/include/boost/units/conversion.hpp#L167  
Unless I'm mistaken, the user doesn't have a choice here.  
  
I opened https://svn.boost.org/trac/boost/ticket/11389 about this issue.

---

## Comment 4

> Username: jhunold  
> Created_at: 2015-06-25 17:47:46 UTC  
> Url: https://github.com/boostorg/units/pull/18#issuecomment-115342599  

@rleigh-dundee the original _double_ is okay as it is a POD and is subject to the usual decaying rules for PODs. Using a _double_ as a template parameter triggers template instantiation rules which opens up a different can of worms . So the hardcoding of _double_ is not a separate issue, but a part of the original issue [#6893](https://svn.boost.org/trac/boost/ticket/6893). Those are the reasons I want an unit test case which shows that different constants are used for different underlying floating types and makes sure that this stays so.

---

## Comment 5

> Username: hernando  
> Created_at: 2023-04-11 15:02:56 UTC  
> Updated_at: 2023-04-11 15:03:30 UTC  
> Url: https://github.com/boostorg/units/pull/18#issuecomment-1503550307  

Is #44 a valid solution? The current constants do not have acceptable precision when using double.

---
