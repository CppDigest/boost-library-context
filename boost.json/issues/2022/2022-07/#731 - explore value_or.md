# #731 - explore value_or [Open]

> Username: vinniefalco  
> Created at: 2022-07-22 21:08:22 UTC  
> Updated at: 2024-10-09 15:29:18 UTC  
> Url: https://github.com/boostorg/json/issues/731  

We can try exploring this signature:  
```  
value object::value_or( string_view key, value_ref v );  
```  
  
Or if there are problems with constructing basic types we could do  
```  
template<class T>  
value object::value_or( string_view key, T const& t )  
{  
  value_ref v(t);  
  ...  
```  
    
This would inhibit arrays and objects from being used as the alternate value but that might be desired.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-09 15:29:16 UTC  
> Url: https://github.com/boostorg/json/issues/731#issuecomment-2402659093  

We can now do this: https://godbolt.org/z/a7nebe5ee  
  
We might even get to `jo.try_at(k) | def_value` soon.
