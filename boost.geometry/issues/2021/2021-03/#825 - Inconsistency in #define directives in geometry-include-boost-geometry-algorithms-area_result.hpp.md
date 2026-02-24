# #825 - Inconsistency in #define directives in geometry/include/boost/geometry/algorithms/area_result.hpp [Closed]

> Username: ayushgupta138  
> Created at: 2021-03-05 10:22:45 UTC  
> Updated at: 2021-06-03 14:29:15 UTC  
> Closed at: 2021-03-05 13:23:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/825  

As per the developer guidelines https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers ,   
 Every header shall have `#include` guard based on header path and file name:  
`#ifndef BOOST_GEOMETRY_<DIR1>_<DIR2>_<FILE>_HPP`  
`#define BOOST_GEOMETRY_<DIR1>_<DIR2>_<FILE>_HPP`  
area_result.hpp begins with  
https://github.com/boostorg/geometry/blob/6918276567a16d1699f5e27f05d9bcd7c6512a43/include/boost/geometry/algorithms/area_result.hpp#L13  
https://github.com/boostorg/geometry/blob/6918276567a16d1699f5e27f05d9bcd7c6512a43/include/boost/geometry/algorithms/area_result.hpp#L14  
instead of   
`#ifndef BOOST_GEOMETRY_ALGORITHMS_AREA_RESULT_HPP`  
`#define BOOST_GEOMETRY_ALGORITHMS_AREA_RESULT_HPP`

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-03-05 12:43:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/825#issuecomment-791396117  

Good catch!
