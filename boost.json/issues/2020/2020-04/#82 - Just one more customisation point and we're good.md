# #82 - Just one more customisation point and we're good. [Closed]

> Username: madmongo1  
> Created at: 2020-04-28 06:46:18 UTC  
> Updated at: 2020-06-15 17:52:22 UTC  
> Closed at: 2020-06-15 17:52:21 UTC  
> Url: https://github.com/boostorg/json/issues/82  

Boost.JSON currently has two customisation points for the serialisation of UDTs to JSON values:  
  
* provide a member function `T::to_json(boost::json::value&) const`  
* specialise the template `boost::json::to_value_traits<>` in the namespace `boost::json`.  
  
This issues lays out an argument why, in the author's view, there is a need for one more.  
  
### It is not possible to automatically provide a conversion for enums with the use of a macro.  
  
It is common practice to use one of the recent, very useful header-only mini-libraries to provide missing utility to c++ enum types. One example of this is wise_enum (https://github.com/quicknir/wise_enum) although there are a few.  
  
By declaring an enum with the `WISE_ENUM_XXX` macros, users gain proper c++ enums wich have full compile-time reflection. For example `wise_enum::to_string(E)` yields a `string_view` and `wise_enum::from_string<T>(str)` yields an `optional<E>`.  
  
This has great utility in logging/serialisation etc.  
  
I go further in my programs and provide an ADL `to_string` overload for all object types, essentially providing:  
  
```  
template< class E, wise_enum::is_wise_enum_v<E>* = nullptr >  
string_view   
to_string(  
    E const& e)    
{  
    return wise_enum::to_string(e);  
}  
```  
as part of the macro which declares the enum. Other such mini-functions are printed by this macro in order to provide `operator<<` and `operator>>`, which means, for example, that the enum is now parsable by boost::program_options as an option name rather than an integer.  
  
The provision of an ADL version simplifies the writing of generic code, for example:  
  
```  
    template<class Impl>  
    struct packet_enable_json  
    {  
        void to_json(boost::json::value& jv) const  
        {  
            auto& self = static_cast<Impl const&>(*this);  
            auto& object = jv.emplace_object();  
            object.reserve(2);  
            object.emplace("type", to_string(self.id()));   // <<== HERE  
            object.emplace("data", boost::json::to_value(Impl::as_nvps(self), object.storage()));  
        }  
    };  
```  
  
In the above real example, any `Impl` who's `.id()` method yields any type which has the ADL `to_string` function available will be correctly serialised to json.  
  
This works when the objects are always represented as JSON strings. But in the above example I have a problem if the result type of `Impl::as_nvps(self)` yields an enum.  
  
Currently boost.json will serialise the underlying integer representation (in the case of an old-style enum) or fail to compile with an `enum class`  
  
The solution of course is to provide a specialisation of `boost::json::to_value_traits` but (prior to fully compliant c++20 compilers) I have the problem that my utility macro will need to:  
1. end the current namespace,   
2. open the `boost::json` namespace,   
3. print the specialisation in terms of `wise_enum::to_string` and then,  
4.  re-establish the original namespace in which the enum is declared.   
  
The final step is not possible without futher decorating the macro with namespace names, which is ugly.  
  
###I offer ~three~ two solutions:  
  
1. ~Allow the overloading of `boost::json::to_value` as a customisation point.~ (this doesn't actually help)  
  
2. Add one additional step to the deduction of behaviour of `boost::json::to_value`:  
  
> The library provides a set of customization points to enable conversions between objects of type T and a JSON value by:  
>   
> * A specialization of to_value_traits for T containing the public static member function void to_json_traits<T>::assign( value&, T const& ),  
>   
> * A specialization of value_cast_traits for T containing the public static member function T value_cast_traits<T>::construct( value const& ),  
>   
> * **A free function declared in the namespace of T compatible with the following signature: `void to_json(T const&, boost::json::value& jv , boost::json::storage_ptr = {})`**  
>   
> * A public non-static member function void T::to_json( value& ) const,  
>   
> * A public constructor T::T( value const& ),  
  
3. Add an additional `Enable` template parameter to `to_value_traits` so that user programs can partially specialise the trait for all types which match a predicate.  
  
For example:  
  
```  
template< class Enum >  
struct to_value_traits < Enum, std::enable_if_t< wise_enum::is_wise_enum_v<Enum> > >  
{  
  static void assign( value& jv, Enum const& e )  
  {  
    jv = wise_enum::to_string(e);  
  }  
}  
```  
  
Opinions about ADL are as varied and as strong as opnions about exceptions, almost-always-auto and the use of goto.  
  
I am in favour of the provision of ADL overloads, as they are simple and _unobtrsive_.  
  
However, any of the above solutions would be acceptable to me as they would provide the necessary functionality.  
  
@vinniefalco   
@pdimov   
@sdkrystian

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-28 14:07:39 UTC  
> Updated at: 2020-04-28 14:09:19 UTC  
> Url: https://github.com/boostorg/json/issues/82#issuecomment-620630303  

ADL overloads are not possible because the library reserves generic overloads for itself, e.g.  
https://github.com/CPPAlliance/json/blob/develop/include/boost/json/detail/to_value.hpp#L68  
  
We made this decision after evaluating the alternatives (which were worse). Thus there is no ADL style customization point for `to_value`.

---

## Comment 2

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-29 22:58:22 UTC  
> Updated at: 2020-05-29 23:00:05 UTC  
> Url: https://github.com/boostorg/json/issues/82#issuecomment-636227885  

If C++20 is available for you then you can use partial specialization with Concepts feature, e.g.:  
```  
template< class Enum > requires wise_enum::is_wise_enum_v<Enum>  
struct to_value_traits < Enum >  
{  
  static void assign( value& jv, Enum const& e )  
  {  
    jv = wise_enum::to_string(e);  
  }  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-29 23:53:26 UTC  
> Url: https://github.com/boostorg/json/issues/82#issuecomment-636239958  

We are refactoring the interface to value exchange, progress here (comments welcome!)  
https://github.com/CPPAlliance/json/pull/103

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-09 23:13:32 UTC  
> Url: https://github.com/boostorg/json/issues/82#issuecomment-641628252  

Have a look now, I believe these changes were merged to _develop_ ?

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-06-15 17:52:21 UTC  
> Url: https://github.com/boostorg/json/issues/82#issuecomment-644279487  

Tested here: https://github.com/madmongo1/boost-json-enums  
  
Looking good.
