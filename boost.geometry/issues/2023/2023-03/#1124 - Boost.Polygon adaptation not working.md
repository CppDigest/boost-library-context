# #1124 - Boost.Polygon adaptation not working [Open]

> Username: MPcoreDev  
> Created at: 2023-03-17 15:29:31 UTC  
> Updated at: 2025-01-09 08:14:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/1124  

I am trying to use 'simplify' algo on a Boost.Polygon and it fails.  
Please find the link to the oneline example of the issue: https://godbolt.org/z/hYofEbr68  
  
The error is:  
`/opt/compiler-explorer/libs/boost_1_81_0/boost/geometry/algorithms/simplify.hpp:606:29: error: cannot bind non-const lvalue reference of type 'boost::geometry::adapt::bp::ring_proxy<boost::polygon::polygon_with_holes_data<int> >&' to an rvalue of type 'boost::geometry::ring_return_type<boost::polygon::polygon_with_holes_data<int> >::type' {aka 'boost::geometry::adapt::bp::ring_proxy<boost::polygon::polygon_with_holes_data<int> >'}`

---

## Comment 1

> Username: JD31  
> Created at: 2023-03-17 15:53:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1124#issuecomment-1474048425  

I have the same issue with the mapbox::geometry to boost::geometry adapter.  
(The adapter code is here: https://github.com/mapbox/spatial-algorithms/blob/master/include/mapbox/geometry/algorithms/detail/boost_adapters.hpp)

---

## Comment 2

> Username: vissarion  
> Created at: 2023-03-22 10:31:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1124#issuecomment-1479299279  

Thanks for the report. After some quick workarounds I am getting   
`error: no matching function for call to ‘swap(const boost::polygon::point_data<double>&, const boost::polygon::point_data<double>&)’`  
triggered from https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/simplify.hpp#L537  
Maybe the begin and end operators of boost::polygon return a constant reference. It needs some further investigation.

---

## Comment 3

> Username: JD31  
> Created at: 2025-01-09 08:14:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1124#issuecomment-2579406303  

@vissarion By chance do you have a branch or patch with the work you did so far ?    
This issue is really problematic for us, and I can search for a solution based on your work. (I tried to correct by myself from scratch but I am not well skilled with template programming unfortunatly.)
