# #353 - tidy up parse- names [Closed]

> Username: vinniefalco  
> Created at: 2022-08-06 04:05:28 UTC  
> Updated at: 2022-09-11 23:59:07 UTC  
> Closed at: 2022-09-11 23:59:07 UTC  
> Url: https://github.com/boostorg/url/issues/353  

`parse_path` and `parse_query_params` are not consistent. We have many choices  
  
```  
parse_path  
parse_segments  
parse_path_segments  
parse_query  
parse_query_params  
parse_params  
```  
  
but we should choose a pair of functions that are consistent with the rest of the names and return types.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:59:07 UTC  
> Url: https://github.com/boostorg/url/issues/353#issuecomment-1243072771  

they should be consistent now, we have `parse_path` and `parse_params`. In the context of html forms, "params" is correct.
