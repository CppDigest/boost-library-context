# #696 - Remove C++11 build jobs from CI [Closed]

> Username: mloskot  
> Created at: 2022-06-27 08:17:30 UTC  
> Updated at: 2022-06-27 20:45:25 UTC  
> Closed at: 2022-06-27 20:45:25 UTC  
> Url: https://github.com/boostorg/gil/issues/696  

For Boost 1.80, we have announced the [switch from C++11 to C++14 with Boost 1.80](https://github.com/boostorg/gil/pull/677).  
  
This should be followed with removal of C++11 builds from our CI-s.  
  
Yes, GIL may still build as C++11 for a while after the release of Boost 1.80, but keeping those jobs will be confusing.  
Removing them will also speed up our CI operations.

---

## Comment 1

> Username: marco-langer  
> Created at: 2022-06-27 15:02:55 UTC  
> Url: https://github.com/boostorg/gil/issues/696#issuecomment-1167466634  

Shouldn't this be on the 1.81 milestone then?  
  
> Yes, GIL may still build as C++11 for a while after the release of Boost 1.80  
  
Bold to assume that, I thought to get rid of all `std::bind`'s as soon as we hit 1.80, replace them with generic lambdas and never look back ;)

---

## Comment 2

> Username: mloskot  
> Created at: 2022-06-27 16:24:01 UTC  
> Url: https://github.com/boostorg/gil/issues/696#issuecomment-1167571203  

Why? If Boost.GIL 1.80 is said and set to require C++14 or later, then what is the reason to keep anything C++11?
