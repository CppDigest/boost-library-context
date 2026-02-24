# #624 - for_each_segment leads to infinite loop on empty ring [Closed]

> Username: henzim  
> Created at: 2019-10-09 12:48:48 UTC  
> Updated at: 2019-10-13 15:23:31 UTC  
> Closed at: 2019-10-13 15:23:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/624  

I wanted to use  `for_each_segment` in a simple (DXF) drawing function for polygons.   
The function should loop over all segments of the outer ring and inner rings and draw them  
  
It seems that for_each_segment does not work as (I) expected, when used on rings. Empty rings lead to inifinte loop (and crashes if one tries to access the first or second point of the respective segment)  
  
https://wandbox.org/permlink/Y7YrBgAcCHOYy4Zu

---

## Comment 1

> Username: mloskot  
> Created at: 2019-10-09 19:07:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/624#issuecomment-540144987  

Could you see if #625 fixes the problem?  
  
p.s. Please, always state version of Boost (Boost.Geometry) explicitly in the issue description (not indirectly, asking to fish for it on an external linked page like `-I/opt/wandbox/boost-1.71.0/gcc-head/include` on the wandbox.
