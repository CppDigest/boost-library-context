# #10 Do not include windows.h in header only mode [Closed]

> Username: jlodos  
> Created at: 2016-02-29 13:45:49 UTC  
> Updated at: 2017-06-04 15:30:25 UTC  
> Closed at: 2017-06-04 15:30:25 UTC  
> Url: https://github.com/boostorg/system/pull/10  



---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2017-01-10 15:24:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/10#pullrequestreview-15938400  

📁 include/boost/system/detail/error_code.ipp

```diff
 391 |         }
 391 |-         else if ( ::GetLastError() != ERROR_INSUFFICIENT_BUFFER )
 392 |+         else if (boost::detail::winapi::GetLastError() != ERROR_INSUFFICIENT_BUFFER)
```

> Username: Lastique  
> Created_at: 2017-01-10 15:24:53 UTC  
> Url: https://github.com/boostorg/system/pull/10#discussion_r95385726  

Error codes should also be converted to Boost.WinAPI: `ERROR_INSUFFICIENT_BUFFER` => `boost::detail::winapi::ERROR_INSUFFICIENT_BUFFER_`.

> Username: Beman  
> Created_at: 2017-06-04 14:51:01 UTC  
> Url: https://github.com/boostorg/system/pull/10#discussion_r120011923  

Fixed.  
  
Thanks,  
  
--Beman


---

## Comment 2

> Username: Beman  
> Created_at: 2017-06-04 15:30:25 UTC  
> Url: https://github.com/boostorg/system/pull/10#issuecomment-306047184  

Fixed via local merge.  
  
Thanks,  
  
--Beman

---
