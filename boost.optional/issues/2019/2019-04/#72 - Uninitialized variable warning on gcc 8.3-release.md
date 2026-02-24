# #72 - Uninitialized variable warning on gcc 8.3/release [Closed]

> Username: djarek  
> Created at: 2019-04-15 02:45:35 UTC  
> Updated at: 2019-05-05 21:44:43 UTC  
> Closed at: 2019-05-05 21:44:43 UTC  
> Url: https://github.com/boostorg/optional/issues/72  

gcc-8.3 in release mode reports an uninitialized variable warning in Boost.Beast:  
https://travis-ci.org/boostorg/beast/jobs/519772855#L1557  
  
This is the variable whose member gcc considers to be uninitialized:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/read.hpp#L667  
  
I've been able to determine that it's optional's `aligned_storage` (https://github.com/boostorg/optional/blob/develop/include/boost/optional/detail/optional_aligned_storage.hpp#L29) that is causing trouble, because removing `BOOST_MAY_ALIAS` and undefining `BOOST_OPTIONAL_DETAIL_USE_ATTRIBUTE_MAY_ALIAS` makes the warning go away.  
  
Reproduces with the following settings:  
`./b2 libs/beast/test/beast/websocket variant=release toolset=gcc-8.3 link=static cxxstd=17 -j2`

---

## Comment 1

> Username: akrzemi1  
> Created at: 2019-04-15 14:16:45 UTC  
> Url: https://github.com/boostorg/optional/issues/72#issuecomment-483270031  

Thanks. It is likely that it is a manifestation of this problem: https://www.boost.org/doc/libs/1_70_0/libs/optional/doc/html/boost_optional/tutorial/gotchas/false_positive_with__wmaybe_uninitialized.html  
which was determined to be a bug in GCC: it gives a false positive. Does the workaround in the linked page make the warning go away?

---

## Comment 2

> Username: djarek  
> Created at: 2019-04-16 17:40:29 UTC  
> Url: https://github.com/boostorg/optional/issues/72#issuecomment-483773327  

The workaround is a bit hard to apply, because we use `optional` there to avoid declaring a variable in the context it's initialized in (because it's in a `switch`.. `case`). I'll see if I can find an alternative.
