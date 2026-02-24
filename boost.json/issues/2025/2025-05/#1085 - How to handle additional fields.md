# #1085 - How to handle additional fields [Closed]

> Username: octopus-prime  
> Created at: 2025-05-24 13:11:11 UTC  
> Updated at: 2025-05-27 11:13:22 UTC  
> Closed at: 2025-05-27 11:13:21 UTC  
> Url: https://github.com/boostorg/json/issues/1085  

A common usecase is to define a DTO with a subset of the JSON fields.  
Lets say a generic endpoint delivers a JSON with 10 fields - but i only need 4 of them.  
  
The current observation is, that value_to stops working when dealing with additional (unknown) fields.  
Instead of ignoring them.  
  
How to deal with that?  
  
Example  
```  
#include <boost/describe/class.hpp>  
#include <boost/json/value_to.hpp>  
#include <boost/json/parse.hpp>  
#include <boost/static_string.hpp>  
#include <iostream>  
  
struct driver_dto {  
    std::int64_t driver_number;  
    boost::static_string<32> first_name;  
    boost::static_string<32> last_name;  
    boost::static_string<32> full_name;  
    boost::static_string<32> broadcast_name;  
    boost::static_string<3> country_code;  
};  
  
BOOST_DESCRIBE_STRUCT(driver_dto, (), (driver_number, first_name, last_name, full_name, broadcast_name, country_code))  
  
void parse_and_convert_and_print(std::string_view prefix, std::string_view json_text) {  
    auto value = boost::json::parse(json_text);  
    auto result = boost::json::try_value_to<driver_dto>(value);  
    if (result) {  
        std::cout << prefix <<  " Driver: " << result->first_name << " " << result->last_name << ", Number: " << result->driver_number << "\n";  
    } else {  
        std::cout << prefix << " Failed to parse driver data.\n";  
    }  
}  
  
int main() {  
    const std::string_view ok = R"({  
        "broadcast_name": "M VERSTAPPEN",  
        "country_code": "NED",  
        "driver_number": 1,  
        "first_name": "Max",  
        "full_name": "Max VERSTAPPEN",  
        "last_name": "Verstappen"  
    })";  
    const std::string_view broken_because_of_additional_fields = R"({  
        "broadcast_name": "M VERSTAPPEN",  
        "country_code": "NED",  
        "driver_number": 1,  
        "first_name": "Max",  
        "full_name": "Max VERSTAPPEN",  
        "last_name": "Verstappen",  
        "meeting_key": 1219,  
        "name_acronym": "VER",  
        "session_key": 9158,  
        "team_colour": "3671C6",  
        "team_name": "Red Bull Racing"  
    })";  
  
    parse_and_convert_and_print("ok", ok);  
    parse_and_convert_and_print("broken", broken_because_of_additional_fields);  
  
    return 0;  
}  
```  
->  
```  
ok Driver: Max Verstappen, Number: 1  
broken Failed to parse driver data.  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-05-26 08:36:30 UTC  
> Url: https://github.com/boostorg/json/issues/1085#issuecomment-2908968044  

This example doesn't fail for me locally. Are you using an older version of the library?

---

## Comment 2

> Username: octopus-prime  
> Created at: 2025-05-26 08:56:17 UTC  
> Url: https://github.com/boostorg/json/issues/1085#issuecomment-2909005982  

Very nice!  
  
I have to use boost 1.83 (newest in ubuntu 24.04).  
The AI told me that the bahavior changed in boost 1.87 - right?  
So i will wait for 1.87  
  
Thanks :-)

---

## Comment 3

> Username: octopus-prime  
> Created at: 2025-05-26 08:59:31 UTC  
> Url: https://github.com/boostorg/json/issues/1085#issuecomment-2909011095  

I found this https://www.boost.org/doc/libs/1_83_0/libs/describe/doc/html/describe.html#example_from_json  
  
So until 1.87 i can workaround with something like  
  
```  
template<class T> void extract( boost::json::value const & val, char const * name, T & value )  
{  
    value = boost::json::value_to<T>( val );  
}  
  
template<class T,  
    class D1 = boost::describe::describe_members<T,  
        boost::describe::mod_public | boost::describe::mod_protected>,  
    class D2 = boost::describe::describe_members<T, boost::describe::mod_private>,  
    class En = std::enable_if_t<boost::mp11::mp_empty<D2>::value && !std::is_union<T>::value> >  
  
    T tag_invoke( boost::json::value_to_tag<T> const&, boost::json::value const& v )  
{  
    auto const& obj = v.as_object();  
  
    T t{};  
  
    boost::mp11::mp_for_each<D1>([&](auto D){  
        if constexpr (std::__is_std_optional<std::remove_cvref_t<decltype(t.*D.pointer)>>::value) {  
            if( auto ptr = obj.if_contains(D.name) )  
                extract(*ptr, D.name, t.*D.pointer);            
        } else {  
            extract(obj.at(D.name), D.name, t.*D.pointer);  
        }  
    });  
  
    return t;  
}  
```

---

## Comment 4

> Username: grisumbras  
> Created at: 2025-05-26 09:11:28 UTC  
> Url: https://github.com/boostorg/json/issues/1085#issuecomment-2909027282  

Yes, this will work. It has some simplifications, though. You could copy the implementation from Boost.JSON's source: https://github.com/boostorg/json/blob/develop/include/boost/json/detail/value_to.hpp#L349-L427

---

## Comment 5

> Username: octopus-prime  
> Created at: 2025-05-27 11:13:21 UTC  
> Url: https://github.com/boostorg/json/issues/1085#issuecomment-2912128488  

Thanks :-)
