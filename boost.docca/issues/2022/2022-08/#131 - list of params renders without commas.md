# #131 - list of params renders without commas [Closed]

> Username: vinniefalco  
> Created at: 2022-08-28 05:07:54 UTC  
> Updated at: 2022-08-31 23:15:14 UTC  
> Closed at: 2022-08-31 23:15:14 UTC  
> Url: https://github.com/boostorg/docca/issues/131  

```  
@param r,g,b The color components to use  
```  
  
renders as `rgb` instead of `r, g, b`. I've never used multiple variables in one `@param` before so this is probably why it doesn't work.
