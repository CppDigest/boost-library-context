# #18 - wrong reference targets with protected special members [Open]

> Username: vinniefalco  
> Created at: 2017-03-27 19:49:34 UTC  
> Updated at: 2017-03-27 19:49:34 UTC  
> Url: https://github.com/boostorg/docca/issues/18  

For example:  
```  
class T  
{  
protected:  
  T() = default;  
  
public:  
   T(T const&) = delete;  
   T& operator=(T const&) = delete;  
};  
```  
  
This will generate the wrong link targets
