# #65 fix search example [Closed]

> Username: pointertoeric  
> Created at: 2019-11-27 04:15:00 UTC  
> Updated at: 2019-11-27 23:46:15 UTC  
> Closed at: 2019-11-27 23:46:15 UTC  
> Url: https://github.com/boostorg/algorithm/pull/65  

search example code should be updated like:   
`  if ( ba::boyer_moore_search ( haystack.begin (), haystack.end (), needle1.begin (), needle1.end ()) != std::make_pair(haystack.end(), haystack.end()))  
`  
use `std::make_pair(haystack.end(), haystack.end())` instead of `haystack.end()`

---
