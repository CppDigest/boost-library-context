# #736 - Add a nonthrowing overload for value_to [Closed]

> Username: pdimov  
> Created at: 2022-08-03 08:30:47 UTC  
> Updated at: 2022-10-14 17:13:45 UTC  
> Closed at: 2022-10-14 17:13:45 UTC  
> Url: https://github.com/boostorg/json/issues/736  

Implementing `value_to` for `variant` (for one possible representation) involves repeatedly trying `value_to` for each alternative. Since `value_to` reports failures via exceptions, this will be _slow_. It would be better if `value_to` had a `noexcept` overload that could be used for this purpose.  
  
There's no perfect option here, because returning `result<T>` would impose a dependency on System, and we want people to be able to add JSON support without a dependency. The other alternative  
```  
bool value_to( value const& jv, T& t, error_code& ec );  
```  
would require `T` to be default-constructible, but this may be good enough, as the "traditional" `value_to` can be used for other types.  
  
A default implementation for this overload would be  
  
```  
bool value_to( value const& jv, T& t, error_code& ec )  
{  
    try  
    {  
        t = value_to( jv );  
        return true;  
    }  
    catch( system_error const& x )  
    {  
        ec = x.code();  
    }  
    catch( ... )  
    {  
        ec = ...;  
    }  
  
    return false;  
}  
```  
  
but "basic" types should have proper nonthrowing overloads.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-08-07 08:49:25 UTC  
> Url: https://github.com/boostorg/json/issues/736#issuecomment-1207360477  

See also https://github.com/boostorg/json/issues/667.
