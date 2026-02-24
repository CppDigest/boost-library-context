# #359 - custom tuple implementation is conditional [Closed]

> Username: vinniefalco  
> Created at: 2022-08-07 00:07:22 UTC  
> Updated at: 2022-08-07 22:48:14 UTC  
> Closed at: 2022-08-07 22:48:14 UTC  
> Url: https://github.com/boostorg/url/issues/359  

We could selectively use `<tuple>` instead of our slim tuple, on C++ versions / compilers that give us the `constexpr` and empty base optimization that we need.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-07 22:48:14 UTC  
> Url: https://github.com/boostorg/url/issues/359#issuecomment-1207499982  

Perhaps there is not much to be gained here since we would be effectively be creating an extra implementation combination that needs testing
