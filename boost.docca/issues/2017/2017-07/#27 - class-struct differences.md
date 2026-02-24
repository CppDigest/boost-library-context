# #27 - class/struct differences [Open]

> Username: vinniefalco  
> Created at: 2017-07-08 21:44:20 UTC  
> Updated at: 2017-07-08 21:44:27 UTC  
> Url: https://github.com/boostorg/docca/issues/27  

This shows the nested types:  
```  
/// Brief  
struct X  
{  
    /// R  
    using R = ...;  
};  
```  
  
This doesn't  
```  
/// Brief  
class X  
{  
public:  
    /// R  
    using R = ...;  
};  
```
