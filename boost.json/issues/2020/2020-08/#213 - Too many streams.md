# #213 - Too many streams [Open]

> Username: vinniefalco  
> Created at: 2020-08-27 20:12:14 UTC  
> Updated at: 2021-03-04 05:35:53 UTC  
> Url: https://github.com/boostorg/json/issues/213  

There are just too many here:  
  
https://github.com/CPPAlliance/json/blob/develop/include/boost/json/detail/stream.hpp  
  
Surely they are not all needed...

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-28 01:18:13 UTC  
> Url: https://github.com/boostorg/json/issues/213#issuecomment-682270758  

We can eventually transition `serializer` to use the same `const_stream` as `basic_parser` and then remove the old versions.
