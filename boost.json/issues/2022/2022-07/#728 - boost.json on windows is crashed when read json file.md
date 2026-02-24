# #728 - boost.json on windows is crashed when read json file [Closed]

> Username: kwarehit  
> Created at: 2022-07-06 14:01:12 UTC  
> Updated at: 2022-12-18 08:36:00 UTC  
> Closed at: 2022-12-18 08:36:00 UTC  
> Url: https://github.com/boostorg/json/issues/728  

My developement environment as following:  
System: windows 10 pro  
C++ Compiler: vs2019 16.11.15  
Build boost 1.79 by used to C++ Standard 14 on vs2019 16.11.15  
  
Below codes will be crashed if build it and switch /std::c++20 in boost 1.79 but work well switch to /std::c++14 and /std::c++17  
  
  
```  
#include <iostream>  
#include <deque>  
#include <string>  
#include <map>  
#include <fstream>  
#include <memory>  
#include <type_traits>  
  
#include <boost/json.hpp>  
#include <boost/mp11.hpp>  
#include <boost/describe.hpp>  
  
  
namespace json = boost::json;  
  
      
json::value readJson( std::istream& is, json::error_code& ec )  
{  
    json::stream_parser p;  
    for(std::string line; std::getline( is, line ); )  
    {  
        p.write( line, ec );  
        if (ec)   
        {  
            return nullptr;  
        }  
    }  
  
    p.finish( ec );  
  
    if (ec)   
    {  
        return nullptr;  
    }  
  
    return p.release();  
}  
  
void writeJson( std::ostream& os, json::value const& jv, std::string* indent = nullptr )  
{  
    std::string indent_;  
    if(! indent)  
        indent = &indent_;  
    switch(jv.kind())  
    {  
    case json::kind::object:  
    {  
        os << "{\n";  
        indent->append(4, ' ');  
        auto const& obj = jv.get_object();  
        if(! obj.empty())  
        {  
            auto it = obj.begin();  
            for(;;)  
            {  
                os << *indent << json::serialize(it->key()) << " : ";  
                writeJson(os, it->value(), indent);  
                if(++it == obj.end())  
                    break;  
                os << ",\n";  
            }  
        }  
        os << "\n";  
        indent->resize(indent->size() - 4);  
        os << *indent << "}";  
        break;  
    }  
  
    case json::kind::array:  
    {  
        os << "[\n";  
        indent->append(4, ' ');  
        auto const& arr = jv.get_array();  
        if(! arr.empty())  
        {  
            auto it = arr.begin();  
            for(;;)  
            {  
                os << *indent;  
                writeJson( os, *it, indent);  
                if(++it == arr.end())  
                    break;  
                os << ",\n";  
            }  
        }  
        os << "\n";  
        indent->resize(indent->size() - 4);  
        os << *indent << "]";  
        break;  
    }  
  
    case json::kind::string:  
    {  
        os << json::serialize(jv.get_string());  
        break;  
    }  
  
    case json::kind::uint64:  
        os << jv.get_uint64();  
        break;  
  
    case json::kind::int64:  
        os << jv.get_int64();  
        break;  
  
    case json::kind::double_:  
        os << jv.get_double();  
        break;  
  
    case json::kind::bool_:  
        if(jv.get_bool())  
            os << "true";  
        else  
            os << "false";  
        break;  
  
    case json::kind::null:  
        os << "null";  
        break;  
    }  
  
    if(indent->empty())  
        os << "\n";  
}  
  
template<class T>   
void extract( json::object const & obj, char const * name, T & value )  
{  
    value = json::value_to<T>( obj.at( name ) );  
}  
  
template<  
    typename T,  
    typename D1 = boost::describe::describe_members<T, boost::describe::mod_public | boost::describe::mod_protected>,  
    typename D2 = boost::describe::describe_members<T, boost::describe::mod_private>,  
    typename En = std::enable_if_t<boost::mp11::mp_empty<D2>::value>   
>  
T tag_invoke( json::value_to_tag<T> const&, json::value const& v )  
{  
    auto const& obj = v.as_object();  
  
    T t{};  
  
    boost::mp11::mp_for_each<D1>([&](auto D)  
    {  
        extract( obj, D.name, t.*D.pointer );  
    });  
  
    return t;  
}  
  
template<  
    typename T,  
    typename D1 = boost::describe::describe_members<T, boost::describe::mod_public | boost::describe::mod_protected>,  
    typename D2 = boost::describe::describe_members<T, boost::describe::mod_private>,  
    typename En = std::enable_if_t<boost::mp11::mp_empty<D2>::value>   
>  
void tag_invoke( json::value_from_tag const&, json::value& v, T const & t )  
{  
    auto& obj = v.emplace_object();  
  
    boost::mp11::mp_for_each<D1>([&](auto D)  
    {  
        obj[ D.name ] = json::value_from( t.*D.pointer );  
    });  
}  
  
class JsonFile   
{  
public:  
      
    template <typename T>  
    static void loadJsonFile(const std::string file, T& info)   
    {  
        try  
        {  
            std::ifstream inFile(file, std::ios::in);  
  
            json::error_code ec;  
            auto val = readJson(inFile, ec);  
            if (ec)  
            {  
                throw json::system_error(ec, "read json file error:");  
            }  
  
            info = json::value_to<T>(val);  
        }  
        catch (std::exception&)  
        {  
            throw;  
        }  
    }  
  
    template <typename T>  
    static void saveJsonFile(const std::string file, const T& info)   
    {  
        try  
        {  
            std::ofstream outFile(file, std::ios::out | std::ios::trunc);  
  
            auto jv = boost::json::value_from(info);  
            writeJson(outFile, jv);  
        }  
        catch (std::exception&)  
        {  
            throw;  
        }  
    }  
};  
  
struct OBACmd  
{  
    std::string command;  
    friend bool operator<(const OBACmd& lValue, const OBACmd& rValue)   
    {  
        return lValue.command < rValue.command;  
    }  
};  
  
BOOST_DESCRIBE_STRUCT(OBACmd, (), (command))  
  
struct OBAInfo   
{  
    std::map<OBACmd, std::deque<OBACmd>> cmdinfo;  
};  
  
BOOST_DESCRIBE_STRUCT(OBAInfo, (), (cmdinfo))  
  
  
void loadFile()  
{  
    try  
    {  
        std::deque<OBAInfo> obaInfo;  
        JsonFile::loadJsonFile("./conf.json", obaInfo);  
    }  
    catch (const std::exception& e)  
    {  
        std::cout << "read error: " << e.what() << std::endl;  
    }  
}  
  
void saveFile()  
{  
    try  
    {  
        OBACmd s1 = {"key"};  
        OBACmd s2 = {"value"};  
  
        OBAInfo a1;  
        a1.cmdinfo.emplace(s1, std::deque<OBACmd>{s2});  
  
        std::deque<OBAInfo> obaInfo = {a1};  
  
        JsonFile::saveJsonFile("./conf.json", obaInfo);  
    }  
    catch (const std::exception& e)  
    {  
        std::cout << e.what() << std::endl;  
    }  
}  
  
int main()  
{  
    saveFile();  
  
    std::cout << "save done" << std::endl;  
  
    loadFile();  
  
    std::cout << "read done" << std::endl;  
}  
```  
Content of json file is here:  
```  
[  
    {  
        "cmdinfo" : [  
            [  
                {  
                    "command" : "key"  
                },  
                [  
                    {  
                        "command" : "value"  
                    }  
                ]  
            ]  
        ]  
    }  
]  
```  
  
output in C++20:  
```  
save done  
read error: not an object  
read done  
```  
  
output in C++14/17:  
```  
save done  
read done  
```  
  
**BTW: In boost 1.78, use same develpment environment this codes is work well whether it's switch to /std::c++20 or /std::c++14 or /std::c++17**

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-07-06 19:06:05 UTC  
> Url: https://github.com/boostorg/json/issues/728#issuecomment-1176574126  

So, the issue is that conversion of your types do not round trips in C++20. See this: https://godbolt.org/z/zo5GedabM  
Four asserts fail in C++17, but 3 fail in C++20. What this means is that in C++17 your ` std::map<OBACmd, std::deque<OBACmd>>` is serialized and parsed as an array, but in C++20 it is serialized as an array, but parser expects an object. IIRC, this is because there are improvements to aggregates' construction in C++20.  
  
The reason this worked with a previous version of the library is because in version 1.79 we switched to a different implementation of `string_view`.  
  
I don't think this is something we can fix in Boost.JSON. You can change your type `OBACmd` to conform to our requirements for keys of maps (so that the map would serialize as an object). In a future release we will very likely make the traits more restrictive, so that your run-time error would become a compile-time error.

---

## Comment 2

> Username: kwarehit  
> Created at: 2022-07-06 23:43:52 UTC  
> Url: https://github.com/boostorg/json/issues/728#issuecomment-1176861308  

thank you for detailed replay

---

## Comment 3

> Username: kwarehit  
> Created at: 2022-07-07 21:38:17 UTC  
> Url: https://github.com/boostorg/json/issues/728#issuecomment-1178255811  

I think it is defect due to switched to C++20, will I close it after you change run-time error to compiler-time error when upgrade to C++20?  
I can't imagine what JSON looks like after use structure to key in std::map and it seems hard to  change the code in order to comform to your requirements for keys of maps (so that the map would serialize as an object)

---

## Comment 4

> Username: kwarehit  
> Created at: 2022-07-08 14:38:56 UTC  
> Updated at: 2022-07-08 23:23:41 UTC  
> Url: https://github.com/boostorg/json/issues/728#issuecomment-1179063951  

After I deeply reseach the implementation of boost::core::string_view and boost::json::value_to, I found structure OBACmd is serialized and parsed as key of maps in json is not what I want, because others member would be missed in seriaized to json format if there is others member in strucure OBACmd, for example  
```  
struct OBACmd  
{  
    std::string command;  
    string others;  
      
    operator boost::json::string_view() const  
    {  
        return command;  
    }  
      
    friend bool operator<(const OBACmd& lValue, const OBACmd& rValue)   
    {  
        return lValue.command < rValue.command;  
    }  
};  
  
BOOST_DESCRIBE_STRUCT(OBACmd, (), (command, others))  
  
struct OBAInfo   
{  
    std::map<OBACmd, std::deque<OBACmd>> cmdinfo;  
};  
  
BOOST_DESCRIBE_STRUCT(OBAInfo, (), (cmdinfo))  
```  
Althought above OBACmd can be successully serialized to keys of map in json, but member others in OBACmd would be missed in json file, in other words, **member others in OBACmd will not be save in json file**, **so std::map<OBACmd, std::deque<OBACmd>> is serialized and parsed as an array in C++20 is what I expected**, **my objective is to make compile-call std::is_constructible<OBACmd, boost::json::string_view>::value is false in C++20**, I found following three ways can be achieve this goal  
  
1. Full specialization template class std::is_constructible, but this way will insert customization template into namespace std:  
```  
namespace std {  
    template<>  
    std::is_constructible<const OBACmd, boost::json::string_view> : std::false_type{};  
}  
```  
but I think this way is ugly  
  
2. Add dummy member as first member to OBACmd to stop to implicit conversion from boost::json::string_view to OBACmd, but I think it's a bit hard to understand and changed the memory alignment as a useless member:  
```  
struct OBACmd  
{  
    char dummy;  
    std::string command;  
    string others;  
  
    friend bool operator<(const OBACmd& lValue, const OBACmd& rValue)   
    {  
        return lValue.command < rValue.command;  
    }  
};  
  
BOOST_DESCRIBE_STRUCT(OBACmd, (), (command, others))  
```  
3. By declare explicit default constructor to disable implicit conversion, but it's also disabled aggregates construction, so need to customized aggregates construction:  
```  
struct OBACmd  
{  
    explicit OBACmd() = default;  
      
    explicit OBACmd(std::initializer_list<std::string> ls)  
    {  
        auto it = ls.begin();  
        command = *it;  
        others = std::next(it); // OBACmd a{"ab", "cd"}; can be work  
    }  
  
    std::string command;  
    string others;  
      
    friend bool operator<(const OBACmd& lValue, const OBACmd& rValue)   
    {  
        return lValue.command < rValue.command;  
    }  
};  
  
BOOST_DESCRIBE_STRUCT(OBACmd, (), (command, others))  
```  
  
I don't know if there are more ways to disable this implicit conversion behavior in C++20

---

## Comment 5

> Username: grisumbras  
> Created at: 2022-07-09 08:47:43 UTC  
> Url: https://github.com/boostorg/json/issues/728#issuecomment-1179507106  

As I've said previously, in a future release (probably 1.81) we will make the traits more restrictive, so your case will start working again in C++20. In addition we do plan to allow users to explicitly disable particular matches for their types.
