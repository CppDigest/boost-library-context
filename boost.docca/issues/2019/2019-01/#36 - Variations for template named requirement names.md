# #36 - Variations for template named requirement names [Open]

> Username: vinniefalco  
> Created at: 2019-01-02 22:05:09 UTC  
> Updated at: 2019-01-04 14:51:27 UTC  
> Url: https://github.com/boostorg/docca/issues/36  

When a template parameter name is customized, for example when `DynamicBuffer` is hyperlinked, standard variations on the name should also resolve to the customization:  
  
These are equivalent:  
  
```  
Handler  
Handler_  
DeducedHandler  
OtherHandler  
```  
  
These rules would be "suffix underscore", prefix "Deduced" or "Other". These rules should be part of the application customization layer (I think).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-04 14:50:41 UTC  
> Updated at: 2019-01-04 14:51:27 UTC  
> Url: https://github.com/boostorg/docca/issues/36#issuecomment-451464452  

Also numbers:  
```  
Executor2  
```  
  
and combinations thereof:  
```  
Executor2_  
OtherExecutor2  
```
