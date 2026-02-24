# #633 - Maybe standalone math missing BOOST_VERSION? [Closed]

> Username: ckormanyos  
> Created at: 2021-05-20 18:33:36 UTC  
> Updated at: 2021-05-20 19:57:23 UTC  
> Closed at: 2021-05-20 19:57:23 UTC  
> Url: https://github.com/boostorg/math/issues/633  

Warning: This issue might be not correct, as it is based on some nexperimental work I'm doing.  
  
I am doing some CI work on non-boost projects that use Boost.Math and Multiprecision.  
  
I'm using develop straight off the submods. I found empirically or somehow got the impression the `BOOST_VERSION` was not available after I included a few math and multiprecision headers, though do admit that I'm not really sure if it should or should not be.  
  
I needed to explicitly `#include <version.hpp>`.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-05-20 18:44:32 UTC  
> Url: https://github.com/boostorg/math/issues/633#issuecomment-845375685  

Ooops.  
I think it was I believe my mistake, as I had tried to use `BOOST_VERSION` prior to including anything from boost.
