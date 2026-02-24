# #269 - review use of <algorithm> [Closed]

> Username: alandefreitas  
> Created at: 2022-07-28 17:17:24 UTC  
> Updated at: 2022-08-05 05:08:34 UTC  
> Closed at: 2022-08-05 05:08:34 UTC  
> Url: https://github.com/boostorg/url/issues/269  

We've been avoiding `<algorithm>` to improve compile times and issues like #210. However, `<boost/core/detail/string_view.hpp>` is including <algorithm> anyway.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-05 01:40:22 UTC  
> Url: https://github.com/boostorg/url/issues/269#issuecomment-1205946457  

This is probably obsolete. #307 already handles #210 and there's nothing we can do about `<boost/core/detail/string_view.hpp>` in this repository.
