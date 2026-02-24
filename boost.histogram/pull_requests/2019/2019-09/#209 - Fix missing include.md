# #209 Fix missing include [Merged]

> Username: henryiii  
> Created at: 2019-09-04 19:42:13 UTC  
> Updated at: 2019-09-09 09:26:07 UTC  
> Merged at: 2019-09-09 09:25:57 UTC  
> Closed at: 2019-09-09 09:25:57 UTC  
> Url: https://github.com/boostorg/histogram/pull/209  

`make_default` is used in this file, but was not included, causing this to break if `make_default.hpp` was not included before it somewhere.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2019-09-09 09:26:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/209#issuecomment-529380994  

Thank you for finding this oversight!

---
