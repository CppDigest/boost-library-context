# #206 - Add parse_options to `parse` free functions [Closed]

> Username: vinniefalco  
> Created at: 2020-08-25 14:38:45 UTC  
> Updated at: 2020-08-27 02:41:25 UTC  
> Closed at: 2020-08-27 02:41:25 UTC  
> Url: https://github.com/boostorg/json/issues/206  

As the last paramete, defaulted to `{}`

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-25 15:20:33 UTC  
> Url: https://github.com/boostorg/json/issues/206#issuecomment-680092029  

Do we want a default parameter or seperate overloads? Default parameter means that we require a `storage_ptr` to be passed if the user wants to specify parse options

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-25 15:25:02 UTC  
> Url: https://github.com/boostorg/json/issues/206#issuecomment-680094726  

Peter says default parameter. Otherwise, this is ambiguous:  
```  
parse( s, ec, {} );  
```

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-08-25 15:26:53 UTC  
> Url: https://github.com/boostorg/json/issues/206#issuecomment-680095930  

Ah, ok
