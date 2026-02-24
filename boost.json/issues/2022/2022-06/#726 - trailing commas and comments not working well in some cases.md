# #726 - trailing commas and comments not working well in some cases [Closed]

> Username: terrywh  
> Created at: 2022-06-30 13:15:55 UTC  
> Updated at: 2022-06-30 15:49:56 UTC  
> Closed at: 2022-06-30 15:49:55 UTC  
> Url: https://github.com/boostorg/json/issues/726  

### Version of Boost  
1.79.0  
  
### Steps necessary to reproduce the problem  
  
``` c++  
#include <boost/json/parse.hpp>  
#include <boost/json/value.hpp>  
  
int main(int argc, char* argv[]) {  
    std::string raw = R"([  
    "123", // 456  
])";  
    boost::json::parse_options opt {};  
    opt.allow_comments = true;  
    opt.allow_trailing_commas = true;  
    boost::json::value val = boost::json::parse(raw, {}, opt);  
    return 0;  
}  
  
```  
  
  
compile :  
``` bash  
g++ -I /data/vendor/boost-1.79/include -L /data/vendor/boost-1.79/lib test.cpp -lboost_json -lboost_system -o test_json  
```  
  
run the program, and it will terminate with the following:   
``` bash  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'  
  what():  syntax error [boost.json:1 at ./boost/json/basic_parser_impl.hpp:634 in function 'parse_value']  
```  
  
either remove the comment ("// 456") or the trailing commas, the exception will go away.  
  
### All relevant compiler information  
gcc/g++ 7.3.1
