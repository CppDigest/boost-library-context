# #358 - get rid of dupe type_traits [Closed]

> Username: vinniefalco  
> Created at: 2020-09-17 21:26:47 UTC  
> Updated at: 2020-09-18 23:55:44 UTC  
> Closed at: 2020-09-18 23:55:44 UTC  
> Url: https://github.com/boostorg/json/issues/358  

The point of having our own small set of type_traits was to not include <utility> but it didn't work out, so we should get rid of the redundant type traits. In the future we can revisit trimming system includes.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-18 23:55:44 UTC  
> Url: https://github.com/boostorg/json/issues/358#issuecomment-695131845  

Done
