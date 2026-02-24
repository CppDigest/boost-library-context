# #21 - Namespace qualifiers in @ref [Open]

> Username: vinniefalco  
> Created at: 2017-05-29 01:38:55 UTC  
> Updated at: 2017-05-29 01:38:55 UTC  
> Url: https://github.com/boostorg/docca/issues/21  

In a **@ref**, the namespace is shown for types but not for functions. For example  
```  
@see @ref async_read_some, @ref serializer  
```  
  
Produces  
  
**See Also: `async_read_some`, `http::serializer`**
