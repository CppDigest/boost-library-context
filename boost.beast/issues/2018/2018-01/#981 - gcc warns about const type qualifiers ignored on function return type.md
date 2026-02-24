# #981 - gcc warns about const type qualifiers ignored on function return type [Closed]

> Username: chrisics  
> Created at: 2018-01-15 12:18:12 UTC  
> Updated at: 2018-01-15 12:38:49 UTC  
> Closed at: 2018-01-15 12:38:49 UTC  
> Url: https://github.com/boostorg/beast/issues/981  

In fields.ipp, line 294 and fields.hpp, line 96:    
  
for instance:  
field const name()    
  
Const does not have an effect on a primitive type as return value. See https://stackoverflow.com/questions/40689158/why-type-qualifiers-ignored-on-function-return-type-only-in-case-of-primitives

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-15 12:34:00 UTC  
> Url: https://github.com/boostorg/beast/issues/981#issuecomment-357670736  

This is fixed in the tip of develop and master - thanks!  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/fields.hpp#L104
