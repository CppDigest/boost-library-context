# #418 - No macro for <version> [Closed]

> Username: pdimov  
> Created at: 2022-02-03 07:21:50 UTC  
> Updated at: 2022-02-04 11:16:37 UTC  
> Closed at: 2022-02-04 11:16:37 UTC  
> Url: https://github.com/boostorg/config/issues/418  

Shouldn't there be BOOST_NO_CXX20_HDR_VERSION?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-02-03 11:43:27 UTC  
> Url: https://github.com/boostorg/config/issues/418#issuecomment-1028907704  

Maybe, but since there's nothing in that header which requires C++20, I've always just used `__has_include(<version>)`.  It gets included by Boost.Config anyway when present BTW.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-02-03 14:21:58 UTC  
> Url: https://github.com/boostorg/config/issues/418#issuecomment-1029038965  

You have to guard the `__has_include` with `#ifdef __has_include`, which makes it less convenient. And yes, it gets always included, and I actually switched to depending on that, but it's technically wrong because this inclusion is not documented and in fact the comment says "do not rely on this, as the user can override it."  
  
I wouldn't mind it being always included to be made a requirement so that we can use the feature macros after `#include <boost/config.hpp>` without checking anything.  
  
But even so, having a HDR macro for every new header _except_ this specific one is a bit inconsistent.
