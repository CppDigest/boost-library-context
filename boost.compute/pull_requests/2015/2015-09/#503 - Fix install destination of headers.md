# #503 Fix install destination of headers [Closed]

> Username: marbre  
> Created at: 2015-09-08 08:39:37 UTC  
> Updated at: 2015-09-09 09:54:22 UTC  
> Closed at: 2015-09-09 09:54:22 UTC  
> Url: https://github.com/boostorg/compute/pull/503  

Without this fix the headers get, at least on my system, installed to `/usr/include/compute/boost/compute` instead of `/usr/include/boost/compute`.

---

## Comment 1

> Username: marbre  
> Created_at: 2015-09-08 10:14:23 UTC  
> Url: https://github.com/boostorg/compute/pull/503#issuecomment-138504906  

`BoostComputeConfig.cmake.in` needs to be modified to if this patch is accepted. Before doing so, I would appreciate any feedback.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-09-09 02:38:21 UTC  
> Url: https://github.com/boostorg/compute/pull/503#issuecomment-138759160  

Yeah, this is the expected behavior. For now, you should install to `/usr/include/compute` and then pass `-I/usr/include/compute` to include the Boost.Compute headers. Once in the standard Boost release (should be soon now), Boost.Compute will be installed with the rest of the Boost libraries under `/usr/include/boost`.

---

## Comment 3

> Username: marbre  
> Created_at: 2015-09-09 09:54:22 UTC  
> Url: https://github.com/boostorg/compute/pull/503#issuecomment-138860281  

Thanks for clarifying. Closing this pull request, as this is the expected behaviour.

---
