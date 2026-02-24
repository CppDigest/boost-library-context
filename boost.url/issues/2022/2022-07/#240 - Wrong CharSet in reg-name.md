# #240 - Wrong CharSet in reg-name? [Closed]

> Username: vinniefalco  
> Created at: 2022-07-23 19:19:06 UTC  
> Updated at: 2022-07-31 03:22:08 UTC  
> Closed at: 2022-07-31 03:22:07 UTC  
> Url: https://github.com/boostorg/url/issues/240  

The code uses  
```  
unreserved_chars + '-' + '.'  
```  
  
but the RFC says  
```  
reg-name      = *( unreserved / pct-encoded / sub-delims )  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-31 03:22:07 UTC  
> Url: https://github.com/boostorg/url/issues/240#issuecomment-1200339738  

Looks like this was fixed in the refactoring  
https://github.com/CPPAlliance/url/blob/5ffeeadb6b6d06975a66cff1316046359ff589ff/include/boost/url/rfc/detail/reg_name_rule.hpp#L48
