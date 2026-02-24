# #64 - search example should be updated [Closed]

> Username: pointertoeric  
> Created at: 2019-11-27 04:04:43 UTC  
> Updated at: 2019-12-03 16:05:12 UTC  
> Closed at: 2019-12-03 16:05:12 UTC  
> Url: https://github.com/boostorg/algorithm/issues/64  

all search examples in https://github.com/boostorg/algorithm/blob/develop/example/search_example.cpp   
should be updated with  
`ba::boyer_moore_search ( haystack.begin (), haystack.end (), needle1.begin (), needle1.end ()) != std::make_pair(haystack.end(), haystack.end())` instead of `haystack.end()` alone.

---

## Comment 1

> Username: mclow  
> Created at: 2019-11-27 18:59:11 UTC  
> Url: https://github.com/boostorg/algorithm/issues/64#issuecomment-559212864  

Updated on develop in 9093abb

---

## Comment 2

> Username: mclow  
> Created at: 2019-12-03 16:05:12 UTC  
> Url: https://github.com/boostorg/algorithm/issues/64#issuecomment-561235885  

Merged to release.
