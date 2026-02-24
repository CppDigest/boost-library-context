# #91 - inconsistent comparison with params [Closed]

> Username: vinniefalco  
> Created at: 2021-12-23 03:33:48 UTC  
> Updated at: 2021-12-26 05:42:04 UTC  
> Closed at: 2021-12-26 05:42:04 UTC  
> Url: https://github.com/boostorg/url/issues/91  

`params`, `params_encoded`, and `params_view` all use `detail::key_equal_encoded` but `params_encoded_view` uses a literal comparison? This doesn't sound right.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-26 05:42:04 UTC  
> Url: https://github.com/boostorg/url/issues/91#issuecomment-1001109476  

I just removed op==, so this is a moot issue
