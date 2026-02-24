# #1736 Fix buffer leak in basic_parser::put [Closed]

> Username: inetic  
> Created at: 2019-10-18 12:00:35 UTC  
> Updated at: 2019-10-20 15:33:38 UTC  
> Closed at: 2019-10-20 15:33:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1736  

Fixes issue #1734

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2019-10-18 12:01:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1736#pullrequestreview-303842167  

📁 include/boost/beast/http/impl/basic_parser.hpp

```diff
  55 |     return put(net::const_buffer{
  56 |-         buf_.get(), buf_len_}, ec);
  56 |+         buf_.get(), size}, ec);
```

> Username: vinniefalco  
> Created_at: 2019-10-18 12:01:21 UTC  
> Updated_at: 2019-10-18 12:01:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1736#discussion_r336455561  

_"That which is untested, contains bugs."_


---
