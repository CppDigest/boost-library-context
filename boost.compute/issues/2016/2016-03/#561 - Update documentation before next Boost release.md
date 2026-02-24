# #561 - Update documentation before next Boost release [Closed]

> Username: kylelutz  
> Created at: 2016-03-03 23:45:06 UTC  
> Updated at: 2016-05-24 02:44:27 UTC  
> Closed at: 2016-05-24 02:44:26 UTC  
> Url: https://github.com/boostorg/compute/issues/561  

Now that Boost.Compute is an official Boost library and will be included in Boost 1.61, we need to update the documentation to reflect this.  
  
Currently there are a few locations where we describe Boost.Compute as ["not yet an offical Boost library"](http://www.boost.org/doc/libs/master/libs/compute/doc/html/boost_compute/getting_started.html).

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-03-04 18:34:00 UTC  
> Url: https://github.com/boostorg/compute/issues/561#issuecomment-192399856  

Are you working on this today? I can delete those notes tomorrow.  
4 mar 2016 00:45 "Kyle Lutz" notifications@github.com napisał(a):  
  
> Now that Boost.Compute is an official Boost library and will be included  
> in Boost 1.61, we need to update the documentation to reflect this.  
>   
> Currently there are a few locations where we describe Boost.Compute as "not  
> yet an offical Boost library"  
> http://www.boost.org/doc/libs/master/libs/compute/doc/html/boost_compute/getting_started.html  
> .  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/561.

---

## Comment 2

> Username: kylelutz  
> Created at: 2016-03-04 18:47:58 UTC  
> Url: https://github.com/boostorg/compute/issues/561#issuecomment-192405905  

Yeah, I've got some work in progress which clean up the docs. Should be ready soon.

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-03-04 18:51:45 UTC  
> Url: https://github.com/boostorg/compute/issues/561#issuecomment-192407349  

Ok 👍  
4 mar 2016 19:48 "Kyle Lutz" notifications@github.com napisał(a):  
  
> Yeah, I've got some work in progress which clean up the docs. Should be  
> ready soon.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/561#issuecomment-192405905.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-03-05 15:16:19 UTC  
> Url: https://github.com/boostorg/compute/issues/561#issuecomment-192670582  

`reduce_by_key` is not listed in API overview.

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-05-14 11:41:14 UTC  
> Updated at: 2016-05-14 11:41:19 UTC  
> Url: https://github.com/boostorg/compute/issues/561#issuecomment-219215749  

I think http://boostorg.github.io/compute/ needs to be rebuilt.

---

## Comment 6

> Username: kylelutz  
> Created at: 2016-05-18 02:59:55 UTC  
> Url: https://github.com/boostorg/compute/issues/561#issuecomment-219912812  

Looks like since becoming a sub-module in the Boost super-project I lost being able to build just the Boost.Compute documentation by itself :-(.  
  
I wonder if we should just have a re-direct from http://boostorg.github.io/compute to http://boost.org/doc/libs/develop/libs/compute/doc/html/index.html (which is automatically updated).
