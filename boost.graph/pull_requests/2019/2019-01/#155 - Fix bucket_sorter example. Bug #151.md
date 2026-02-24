# #155 Fix bucket_sorter example. Bug #151 [Merged]

> Username: deinst  
> Created at: 2019-01-22 19:48:06 UTC  
> Updated at: 2019-01-26 09:26:23 UTC  
> Merged at: 2019-01-26 09:26:23 UTC  
> Closed at: 2019-01-26 09:26:23 UTC  
> Url: https://github.com/boostorg/graph/pull/155  

Changed ID to identity_property_map.  
Added concept check to bucket_sorter.hpp.  
Fixed minor grammar nits.  
  
This is just a fix to #151.  We cannot abandon the bucket_sorter, as it is used by `minimum_degree_ordering.hpp` and  `smallest_last_ordering.hpp`.  I do not think that these changes break either of these, but neither are tested, and only minimum_degree_ordering has an example.  The minimum_degree_ordering example seems to work with some Harwell Boeing files I dredged off the web, but I have little idea whether the results are correct.  
  
Possibly more importantly, BucketSorter is used in the graph coloring example from the book.  
  
This change does show that the ValueType template parameter is redundant, but it is probably not worth removing it.

---
