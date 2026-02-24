# #1443 Register failing test for issue 1439 in intersection framework [Closed]

> Username: kashish2710  
> Created at: 2025-12-14 12:05:38 UTC  
> Updated at: 2025-12-15 05:01:00 UTC  
> Closed at: 2025-12-15 05:01:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1443  

I’ve now integrated the test fully into the framework:  
– case_1439 added to overlay_cases.hpp  
– registered in intersection.cpp with expected area 0.0  
– wrapped in BOOST_GEOMETRY_TEST_ENABLE_FAILING as suggested

---

## Comment 1

> Username: kashish2710  
> Created_at: 2025-12-14 12:15:55 UTC  
> Updated_at: 2025-12-14 12:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1443#issuecomment-3650850734  

This PR includes:  
1. Registers the case in intersection.cpp (expected area 0.0),  
   wrapped in BOOST_GEOMETRY_TEST_ENABLE_FAILING.

---
