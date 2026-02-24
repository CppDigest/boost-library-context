# #17 Disable __thread keyword for android-clang [Closed]

> Username: crystax  
> Created at: 2015-03-24 17:41:20 UTC  
> Updated at: 2015-05-08 15:18:25 UTC  
> Closed at: 2015-05-08 15:18:25 UTC  
> Url: https://github.com/boostorg/asio/pull/17  

Clang on Android don't support __thread properly since it  
require some support from runtime, and Android don't provide it.  
Look at https://tracker.crystax.net/issues/912 for details.

---

## Comment 1

> Username: crystax  
> Created_at: 2015-05-08 15:18:25 UTC  
> Url: https://github.com/boostorg/asio/pull/17#issuecomment-100269954  

This is [already fixed](https://github.com/chriskohlhoff/asio/commit/9c8d15c6b2040f8ad826dd7fb017fb7e21571dff) in upstream repository, so closing this PR.

---
