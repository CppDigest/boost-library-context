# #93 Use throw_exception overload taking source_location [Merged]

> Username: pdimov  
> Created at: 2019-11-26 15:30:18 UTC  
> Updated at: 2019-12-18 12:51:14 UTC  
> Merged at: 2019-11-27 17:01:07 UTC  
> Closed at: 2019-11-27 17:01:07 UTC  
> Url: https://github.com/boostorg/log/pull/93  

`boost::exception_detail::throw_exception_` has been removed.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2019-11-27 16:04:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/log/pull/93#pullrequestreview-323785871  

📁 include/boost/log/detail/adaptive_mutex.hpp

```diff
 220 |-         boost::throw_exception(ExceptionT(err, descr));
 221 |- #endif
 217 |+         boost::throw_exception(ExceptionT(err, descr), boost::source_location(file, line, func));
```

> Username: Lastique  
> Created_at: 2019-11-27 16:04:51 UTC  
> Updated_at: 2019-11-27 16:47:34 UTC  
> Url: https://github.com/boostorg/log/pull/93#discussion_r351373064  

Please, add include of `source_location.hpp`.

> Username: pdimov  
> Created_at: 2019-11-27 16:53:46 UTC  
> Updated_at: 2019-11-27 16:53:47 UTC  
> Url: https://github.com/boostorg/log/pull/93#discussion_r351399774  

Done.


---

## Comment 2

> Username: Lastique  
> Created_at: 2019-11-27 17:01:30 UTC  
> Url: https://github.com/boostorg/log/pull/93#issuecomment-559172824  

Thanks.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-12-18 12:51:14 UTC  
> Url: https://github.com/boostorg/log/pull/93#issuecomment-567018487  

Can be merged to master now.

---
