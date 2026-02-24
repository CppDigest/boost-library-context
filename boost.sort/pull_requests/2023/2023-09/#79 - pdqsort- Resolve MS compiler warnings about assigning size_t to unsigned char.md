# #79 pdqsort: Resolve MS compiler warnings about assigning size_t to unsigned char [Merged]

> Username: nigels-com  
> Created at: 2023-09-11 06:38:40 UTC  
> Updated at: 2023-09-20 09:02:51 UTC  
> Merged at: 2023-09-20 09:02:51 UTC  
> Closed at: 2023-09-20 09:02:51 UTC  
> Url: https://github.com/boostorg/sort/pull/79  

We're seeing lots of MS compiler warnings about pdqsort.  
It looks like using unsigned char for the loop variable is a reasonable alternative.  
  
```  
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(237,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(238,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(239,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(240,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(241,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(242,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(243,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(247,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(253,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(254,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(255,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(256,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(257,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(258,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(259,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(260,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
include\boost-1_82\boost/sort/pdqsort/pdqsort.hpp(264,1): warning C4267: '=': conversion from 'size_t' to 'unsigned char', possible loss of data   
```

---
