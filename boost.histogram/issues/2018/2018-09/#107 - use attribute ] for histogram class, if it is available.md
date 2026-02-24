# #107 - use attribute [[nodiscard]] for histogram class, if it is available [Closed]

> Username: HDembinski  
> Created at: 2018-09-26 08:31:26 UTC  
> Updated at: 2018-11-28 20:49:30 UTC  
> Closed at: 2018-11-28 20:49:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/107  

This would warn the user in the following example  
```  
auto h = make_static_histogram(...);  
std::for_each(data.begin(), data.end(), h); // whoops, h is filled and then discarded  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-11-28 20:49:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/107#issuecomment-442599477  

added in develop
