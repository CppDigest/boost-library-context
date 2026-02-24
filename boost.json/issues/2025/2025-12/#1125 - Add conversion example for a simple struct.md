# #1125 - Add conversion example for a simple struct [Open]

> Username: pdimov  
> Created at: 2025-12-01 17:19:46 UTC  
> Updated at: 2025-12-01 17:19:46 UTC  
> Url: https://github.com/boostorg/json/issues/1125  

E.g.  
```  
struct X  
{  
    std::vector<std::string> a;  
    int b;  
};  
```  
  
Be sure to prominently explain that the natural implementation of `value_from`  
```  
void tag_invoke( boost::json::value_from_tag const&, boost::json::value& jv, X const& x )  
{  
    jv = {  
        { "a", boost::json::value_from( x.a ) },  
        { "b", boost::json::value_from( x.b ) }  
    };  
}  
```  
is wrong because the intermediate calls to `value_from` use the default storage, which introduces an unnecessary copy if `jv` uses a different storage.  
  
Instead, what needs to be written is either  
```  
void tag_invoke( boost::json::value_from_tag const&, boost::json::value& jv, X const& x )  
{  
    jv = {  
        { "a", boost::json::value_from( x.a, jv.storage() ) },  
        { "b", boost::json::value_from( x.b, jv.storage() ) }  
    };  
}  
```  
or  
```  
void tag_invoke( boost::json::value_from_tag const&, boost::json::value& jv, X const& x )  
{  
    jv = {  
        { "a", nullptr },  
        { "b", nullptr }  
    };  
  
    boost::json::value_from( x.a, jv.at( "a" ) );  
    boost::json::value_from( x.b, jv.at( "b" ) );  
}  
```
