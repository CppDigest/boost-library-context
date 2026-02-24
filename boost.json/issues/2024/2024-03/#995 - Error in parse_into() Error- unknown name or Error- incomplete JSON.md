# #995 - Error in parse_into() Error: unknown name or Error: incomplete JSON [Closed]

> Username: ismaelbonato  
> Created at: 2024-03-24 02:26:50 UTC  
> Updated at: 2024-03-24 12:38:44 UTC  
> Closed at: 2024-03-24 12:38:44 UTC  
> Url: https://github.com/boostorg/json/issues/995  

Hello,  
  
I`m trying to deserialize a json file in a structure, it compiles but when a run the code bellow I get the follow error.  
  
  
### error or GCC 11.4  
```cpp  
Error: unknown name [boost.json:39 at xxxx/parse_into.hpp:1144:13 in function 'bool boost::json::detail::converting_handler<boost::json::detail::described_class_conversion_tag, V, P>::on_key(boost::system::error_code&, boost::json::string_view)']  
```  
  
### Error on GCC 12.3  
```cpp  
Error: incomplete JSON [boost.json:3 at xxxxx/json/basic_parser_impl.hpp:2901:17 in function 'std::size_t boost::json::basic_parser<Handler>::write_some(bool, const char*, std::size_t, boost::system::error_code&)']  
```  
  
Thanks for any help.  
  
#### Boost_VERSION is 1.85.0  
#### CMake VERSION is 3.27  
#### g++ (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0 & 12.3.0  
  
  
### Steps necessary to reproduce the problem  
  
my json file:  
```json  
{  
    "Interface": "wlp0s20f3",  
    "Devices": [  
        {  
            "name": "Wemo",  
            "type": "locker",  
            "file": "wemo.xml",  
            "port": 43450,  
            "action": "echo teste"  
        },  
        {  
            "name": "Wemo2",  
            "type": "locker",  
            "file": "wemo.xml",  
            "port": 43450,  
            "action": "echo teste"  
        }  
    ]  
}  
```  
-------------------------------------------------------------------------------------------------------------------  
the code   
  
```cpp  
#include <fstream>  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>  
  
#include <boost/json/src.hpp>  
#include <boost/json.hpp>  
#include <boost/describe/class.hpp>  
  
struct Dev {  
    std::string name;  
    std::string type;  
    std::string file;  
    unsigned int port;  
    std::string action;  
};  
  
BOOST_DESCRIBE_STRUCT(Dev, (), (name, type, file, port, action))  
  
struct configuration {  
    std::string interface;  
    std::vector<Dev> devList;  
};  
  
BOOST_DESCRIBE_STRUCT(configuration, (), (interface, devList))  
  
void func()  
{  
    std::ifstream file("../AlexaWemoEmulator/config.json");  
  
    try {  
        std::string json( std::istreambuf_iterator<char>( file ), std::istreambuf_iterator<char>{} );  
  
        configuration config;  
        boost::system::error_code ec;  
        boost::json::parse_into( config, json, ec );  
  
        if( ec.failed() )  
        {  
            std::cout << "Error: " << ec.what() << std::endl;  
        }  
  
        std::cout << "---------------" << config.interface << std::endl;  
  
  
    }  
    catch(const std::exception& e)  
    {  
        std::cerr << e.what() << '\n';  
    }  
}  
```  
-------------------------------------------------------------------------------------------------------------

---

## Comment 1

> Username: pdimov  
> Created at: 2024-03-24 04:00:54 UTC  
> Url: https://github.com/boostorg/json/issues/995#issuecomment-2016684744  

The names in your struct `configuration` (`interface` and `devList`) do not match the keys in your JSON (`Interface` and `Devices`.) It works if they are changed to match: https://godbolt.org/z/7ffh4zWKh  
  
Also, you don't need these includes  
```  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>  
```

---

## Comment 2

> Username: ismaelbonato  
> Created at: 2024-03-24 12:37:06 UTC  
> Url: https://github.com/boostorg/json/issues/995#issuecomment-2016795898  

> The names in your struct `configuration` (`interface` and `devList`) do not match the keys in your JSON (`Interface` and `Devices`.) It works if they are changed to match: https://godbolt.org/z/7ffh4zWKh  
>   
> Also, you don't need these includes  
>   
> ```  
> #include <boost/property_tree/ptree.hpp>  
> #include <boost/property_tree/json_parser.hpp>  
> ```  
  
Thank you very much, sorry for not paying attention to this silly error, you helped me a lot.
