# #95 - Rewrite scatter()/gather() using meta_kernel [Closed]

> Username: kylelutz  
> Created at: 2014-04-19 17:59:33 UTC  
> Updated at: 2014-05-09 03:30:54 UTC  
> Closed at: 2014-05-09 03:30:54 UTC  
> Url: https://github.com/boostorg/compute/issues/95  

Rewrite the `scatter()` and `gather()` algorithms using `meta_kernel` instead of using a raw source-code string and using string-replacement.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-09 03:30:54 UTC  
> Url: https://github.com/boostorg/compute/issues/95#issuecomment-42630418  

Implemented in d0e5efdbb7038630d3944b613e3beef8dfe91d0d and cd88be2e944423f7c77e3b7109a621db5df6c4f4.
