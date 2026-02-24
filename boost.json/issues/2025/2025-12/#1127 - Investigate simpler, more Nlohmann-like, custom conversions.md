# #1127 - Investigate simpler, more Nlohmann-like, custom conversions [Open]

> Username: pdimov  
> Created at: 2025-12-01 17:37:55 UTC  
> Updated at: 2025-12-05 14:52:58 UTC  
> Url: https://github.com/boostorg/json/issues/1127  

For the type  
```  
struct X  
{  
    std::vector<std::string> a;  
    int b;  
};  
```  
our current solution, if we assume lack of physical dependency on Boost.JSON, is  
```  
namespace boost  
{  
namespace json  
{  
  
class value;  
  
struct value_from_tag;  
  
template<class T>  
void value_from( T&& t, value& jv );  
  
template<class T>  
struct value_to_tag;  
  
template<class T>  
T value_to( value const& jv );  
  
} // namespace json  
} // namespace boost  
  
struct X  
{  
    std::vector<std::string> a;  
    int b;  
  
    template <class JV>  
    friend void tag_invoke( boost::json::value_from_tag const&, JV& jv, X const& x )  
    {  
        jv = {  
            { "a", nullptr },  
            { "b", nullptr }  
        };  
  
        boost::json::value_from( x.a, jv.at( "a" ) );  
        boost::json::value_from( x.b, jv.at( "b" ) );  
    }  
  
    template <class JV>  
    friend X tag_invoke( boost::json::value_to_tag<X> const&, JV const& jv )  
    {  
        return  
        {  
            boost::json::value_to< decltype(X::a) >( jv.at("a") ),  
            boost::json::value_to< decltype(X::b) >( jv.at("b") )  
        };  
    }  
};  
```  
but for nlohmann::json, the above is simply  
```  
struct X  
{  
    std::vector<std::string> a;  
    int b;  
      
    template <class Json>  
    friend void to_json(Json & j, const X & x)  
    {  
        j = {  
            {"a", x.a},  
            {"b", x.b}  
        };  
    }  
      
    template <class Json>  
    friend void from_json(const Json & j, X & x)  
    {  
        x.a = j.at("a");  
        x.b = j.at("b");  
    }  
};  
```  
Note that no forward declarations are needed, because the functionality is fully provided by the JsonValue.  
  
We can achieve something similar if we look for, e.g., `to_boost_json` and `from_boost_json` functions, and make that  
```  
struct X  
{  
    std::vector<std::string> a;  
    int b;  
      
    template <class Json>  
    friend void to_boost_json(Json & j, const X & x)  
    {  
        j = {  
            { "a", x.a },  
            { "b", x.b }  
        };  
    }  
      
    template <class Json>  
    friend void from_boost_json(const Json & j, X & x)  
    {  
        j.at( "a" ).convert_into( x.a );  
        j.at( "b" ).convert_into( x.b );  
    }  
};  
```  
or, to avoid requiring default construction of `X`,  
```  
    template <class Json>  
    friend X from_boost_json(const Json & j )  
    {  
        return  
        {  
            j.at( "a" ).convert_to<decltype(X::a)>(),  
            j.at( "b" ).convert_to<decltype(X::b)>()  
        };  
    }  
```

---

## Comment 1

> Username: pfeatherstone  
> Created at: 2025-12-05 14:52:58 UTC  
> Url: https://github.com/boostorg/json/issues/1127#issuecomment-3617244079  

I agree a lot simpler
