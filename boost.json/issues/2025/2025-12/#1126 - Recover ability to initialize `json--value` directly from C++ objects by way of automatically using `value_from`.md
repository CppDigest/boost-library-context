# #1126 - Recover ability to initialize `json::value` directly from C++ objects by way of automatically using `value_from` [Open]

> Username: pdimov  
> Created at: 2025-12-01 17:24:34 UTC  
> Updated at: 2025-12-01 18:16:15 UTC  
> Url: https://github.com/boostorg/json/issues/1126  

As shown in https://github.com/boostorg/json/issues/1125, the natural implementation of `value_from` for simple structs  
```  
void tag_invoke( boost::json::value_from_tag const&, boost::json::value& jv, X const& x )  
{  
    jv = {  
        { "a", boost::json::value_from( x.a ) },  
        { "b", boost::json::value_from( x.b ) }  
    };  
}  
```  
is subtly inefficient, even though it compiles and works.  
  
To avoid this trap, we should re-enable the functionality of assigning to `json::value` directly, as in  
```  
void tag_invoke( boost::json::value_from_tag const&, boost::json::value& jv, X const& x )  
{  
    jv = {  
        { "a", x.a },  
        { "b", x.b }  
    };  
}  
```  
  
In addition to being correct, this is also much simpler.  
  
This used to work in 1.80 and was intentionally broken in 1.81 (https://github.com/boostorg/json/issues/823) because "it was never supposed to work." (But it probably was supposed to work, for the reason above.)  
  
Note that the solution proposed in #823 has the same problem as the above example.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-12-01 18:16:15 UTC  
> Url: https://github.com/boostorg/json/issues/1126#issuecomment-3598146097  

See also https://github.com/boostorg/json/issues/845 and https://github.com/boostorg/json/issues/917.
