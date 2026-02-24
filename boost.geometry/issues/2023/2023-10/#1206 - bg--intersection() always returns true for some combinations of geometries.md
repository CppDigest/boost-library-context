# #1206 - bg::intersection() always returns true for some combinations of geometries [Open]

> Username: awulkiew  
> Created at: 2023-10-02 10:09:48 UTC  
> Updated at: 2023-10-02 10:10:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1206  

e.g. for Segment*Segment -> Point  
  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/intersection/interface.hpp#L66
