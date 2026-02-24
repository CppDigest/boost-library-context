# #132 - flat_map::lower_bound and upper_bound have wrong/misleading docs. [Closed]

> Username: JAE-UH  
> Created at: 2019-10-10 13:29:39 UTC  
> Updated at: 2019-10-19 20:56:13 UTC  
> Closed at: 2019-10-19 20:55:54 UTC  
> Url: https://github.com/boostorg/container/issues/132  

lower_bound(const key_type & x) says it returns:  
An iterator pointing to the first element with key not less than >>k<<, or >>a.<<end() if such an element is not found.  
  
This has mislabeled variables and should be:  
An iterator pointing to the first element with key not less than >>x<<, or >>end()<< if such an element is not found.  
  
upper_bound(const key_type & x) says it returns:  
An iterator pointing to the first element with key >>not less<< than x, or end() if such an element is not found.  
  
This isn't consistent with the implementation or the usual definition of upper_bound, and should read:  
An iterator pointing to the first element with key >>greater<< than x, or end() if such an element is not found.  
  
This applies to the docs for all the other overloads too.  
  
The relevant part in the current online docs starts here:  
https://www.boost.org/doc/libs/1_71_0/doc/html/boost/container/flat_map.html#idm45836572453456-bb  
  
In repo:  
https://github.com/boostorg/container/blob/d9341ec394ef97bd1b36f05b5e6be6ec538e480c/include/boost/container/flat_map.hpp#L1378

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-10-19 20:56:13 UTC  
> Url: https://github.com/boostorg/container/issues/132#issuecomment-544196646  

Many thanks for the report!
