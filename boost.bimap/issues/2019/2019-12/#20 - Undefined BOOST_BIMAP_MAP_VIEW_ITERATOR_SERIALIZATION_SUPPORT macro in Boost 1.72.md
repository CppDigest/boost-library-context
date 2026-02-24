# #20 - Undefined BOOST_BIMAP_MAP_VIEW_ITERATOR_SERIALIZATION_SUPPORT macro in Boost 1.72 [Open]

> Username: SoapGentoo  
> Created at: 2019-12-20 18:06:40 UTC  
> Updated at: 2019-12-20 18:06:40 UTC  
> Url: https://github.com/boostorg/bimap/issues/20  

I'm filing this bug, coming from https://github.com/lightspark/lightspark/issues/406, which is due to the `BOOST_BIMAP_MAP_VIEW_ITERATOR_SERIALIZATION_SUPPORT` not being defined somewhere along the `#include` stack.
