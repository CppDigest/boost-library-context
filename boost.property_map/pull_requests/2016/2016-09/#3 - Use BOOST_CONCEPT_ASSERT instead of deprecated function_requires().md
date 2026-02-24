# #3 Use BOOST_CONCEPT_ASSERT instead of deprecated function_requires() [Merged]

> Username: alisianoi  
> Created at: 2016-09-11 13:54:28 UTC  
> Updated at: 2016-11-17 02:58:30 UTC  
> Merged at: 2016-11-17 02:58:30 UTC  
> Closed at: 2016-11-17 02:58:30 UTC  
> Url: https://github.com/boostorg/property_map/pull/3  

Currently, `function_requires` is a wrapper around `BOOST_CONCEPT_ASSERT` and is marked for deprecation [by the docs](http://www.boost.org/doc/libs/1_61_0/libs/concept_check/reference.htm#deprecated-functions). This PR substitutes all calls to `function_requires` with calls to `BOOST_CONCEPT_ASSERT` in both code and tests.  
  
Also remove `#include <boost/concept_check.hpp>` from all files (including `parallel_property_maps.hpp`) and leave it only in `property_map.hpp`  
  
This is my first PR to boost, I am just trying to get the hang of it all. Feel free to reject and/or point out problems.

---
