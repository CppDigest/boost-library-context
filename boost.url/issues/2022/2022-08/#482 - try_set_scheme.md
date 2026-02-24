# #482 - try_set_scheme [Open]

> Username: vinniefalco  
> Created at: 2022-08-31 03:57:36 UTC  
> Updated at: 2022-09-27 01:18:28 UTC  
> Url: https://github.com/boostorg/url/issues/482  

For parts which are not encoded, i.e. are not set with `pct_string_view` we need an additional `try_` overload returning `bool` for the case when compiling without exceptions.
