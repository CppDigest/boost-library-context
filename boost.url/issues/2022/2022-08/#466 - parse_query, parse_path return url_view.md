# #466 - parse_query, parse_path return url_view [Closed]

> Username: vinniefalco  
> Created at: 2022-08-27 17:02:17 UTC  
> Updated at: 2022-09-02 17:22:55 UTC  
> Closed at: 2022-09-02 17:22:55 UTC  
> Url: https://github.com/boostorg/url/issues/466  

If we change these functions to simply return `url_view` then the ranges can point to `detail::url_impl` and solve a number of problems

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-02 17:22:55 UTC  
> Url: https://github.com/boostorg/url/issues/466#issuecomment-1235743339  

I simply removed these
