# #540 Minor fixes related to Boost Inspection Report (1.60 develop) [Merged]

> Username: jszuppe  
> Created at: 2015-12-06 17:41:31 UTC  
> Updated at: 2016-05-01 11:19:58 UTC  
> Merged at: 2015-12-06 18:51:36 UTC  
> Closed at: 2015-12-06 18:51:36 UTC  
> Url: https://github.com/boostorg/compute/pull/540  

See http://boost.cowic.de/rc/docs-inspect-develop.html#compute  
  
It fixes:  
  
```  
boost/compute/algorithm/detail/binary_find.hpp: *M* violation of Boost min/max guidelines on line 117, *M* violation of Boost min/max guidelines on line 118, *M* violation of Boost min/max guidelines on line 120, *M* violation of Boost min/max guidelines on line 121  
boost/compute/algorithm/max_element.hpp:  
    (line 27) Non-ASCII: /// \param compare comparison function object which returns ​true if the firs  
boost/compute/algorithm/min_element.hpp:  
    (line 27) Non-ASCII: /// \param compare comparison function object which returns ​true if the firs  
boost/compute/algorithm/minmax_element.hpp:  
    (line 30) Non-ASCII: /// \param compare comparison function object which returns ​true if the firs  
```  
  
If I remember correctly there was a decision that macros will be fix after merging into Boost C++ Libraries, so this pull request does not include commit fixing deprecated macros problem (but it's waiting).

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-12-06 18:51:42 UTC  
> Url: https://github.com/boostorg/compute/pull/540#issuecomment-162336626  

Thanks for fixing these!

---
