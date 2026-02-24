# #531 - value construction from OptionalLike [Closed]

> Username: vinniefalco  
> Created at: 2022-09-11 02:20:15 UTC  
> Updated at: 2022-10-04 00:49:52 UTC  
> Closed at: 2022-10-04 00:49:52 UTC  
> Url: https://github.com/boostorg/url/issues/531  

It should be possible to construct `param`, `param_view`, and `param_pct_view` from any _OptionalLike_

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-26 21:34:36 UTC  
> Url: https://github.com/boostorg/url/issues/531#issuecomment-1258659319  

OptionalLike doesn't exist yet. What would be the difference between OptionalLike and boost::optional?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-26 21:43:57 UTC  
> Url: https://github.com/boostorg/url/issues/531#issuecomment-1258667975  

OptionalLike would also work with `std::optional` but as this is a feature, we should not be implementing it until Later (i.e. not Now)
