# #1208 More fixes for path of compile tests [Merged]

> Username: mborland  
> Created at: 2024-10-10 13:51:15 UTC  
> Updated at: 2024-10-11 18:01:26 UTC  
> Merged at: 2024-10-10 19:43:44 UTC  
> Closed at: 2024-10-10 19:43:44 UTC  
> Url: https://github.com/boostorg/math/pull/1208  



---

## Comment 1

> Username: pdimov  
> Created_at: 2024-10-11 18:01:24 UTC  
> Url: https://github.com/boostorg/math/pull/1208#issuecomment-2407892797  

Wouldn't it be easier to use `INCLUDE_DIRECTORIES ../include_private` in `test/CMakeLists.txt`? That's what `test/Jamfile` does:  
  
https://github.com/boostorg/math/blob/522989011aca84e375e71b3a571007c2fbb9bd6d/test/Jamfile.v2#L75  
  
Also, `boost_test[_jamfile]` supports global variables such as `BOOST_TEST_LINK_LIBRARIES` (and `BOOST_TEST_INCLUDE_DIRECTORIES`) that can be used to factor out the common parts. These are the equivalent of putting things in Jamfile's project requirements.

---
