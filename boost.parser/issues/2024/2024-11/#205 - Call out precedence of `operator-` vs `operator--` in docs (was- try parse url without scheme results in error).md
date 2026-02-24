# #205 - Call out precedence of `operator>` vs `operator>>` in docs (was: try parse url without scheme results in error) [Closed]

> Username: terrywh  
> Created at: 2024-11-21 09:41:40 UTC  
> Updated at: 2024-12-17 06:18:17 UTC  
> Closed at: 2024-12-17 06:18:17 UTC  
> Url: https://github.com/boostorg/parser/issues/205  

i was trying this nice library out, using the following code try to parse a `url` s with all of the  parts optional except the `host` part:  
   
``` c++  
#include <boost/parser/parser.hpp>  
#include <iostream>  
  
namespace bp = boost::parser;  
using namespace std::string_literals;  
  
int main(int argc, char* argv[]) {  
    // auto url = "https://www.qq.com:443/abc/123?a=b&c=d"s;  
    auto url = "www.qq.com"s;  
    if (argc > 1) {  
        url = argv[1];  
    }  
    auto begin = url.begin();  
    auto result = bp::prefix_parse(begin, url.end(),  
        bp::eps  
        >> - (+(bp::char_ - ':') > "://") // an "optional" resulting the error ?  
        >> + (bp::char_ - ':' - '/' - '?')  
        >> - (':' >> bp::int_)  
        >> - ('/' >> +(bp::char_ - '?'))  
        >> - ('?' >> +bp::char_));  
    if (result) {  
        auto scheme = std::get<0>(result.value());  
        if (scheme) {  
            std::cout << "scheme: " << scheme.value() << std::endl;  
        }  
  
        auto host = std::get<1>(result.value());  
        std::cout << "host: " << host << std::endl;  
  
        auto port = std::get<2>(result.value());  
        if (port) {  
            std::cout << "port: " << port.value() << std::endl;  
        }  
  
        auto path = std::get<3>(result.value());  
        if (path) {  
            std::cout << "path: " << path.value() << std::endl;  
        }  
  
        auto query = std::get<4>(result.value());  
        if (query) {  
            std::cout << "query: " << query.value() << std::endl;  
        }  
    }  
    return 0;  
}  
  
```  
  
if i understand correcly, the `operator -` allows an optional matching; but instead after running i get this error output:  
  
```  
1:10: error: Expected "://" here (end of input):  
www.qq.com  
          ^  
```  
  
parsing a url like 'www.qq.com?a=b' will result the following:   
```  
1:14: error: Expected "://" here (end of input):  
www.qq.com?abc'  
```  
  
  
maybe i'm using it wrong ?

---

## Comment 1

> Username: terrywh  
> Created at: 2024-11-22 06:51:56 UTC  
> Url: https://github.com/boostorg/parser/issues/205#issuecomment-2492990406  

Change   
```  
>> - (+(bp::char_ - ':') > "://")  
```  
To  
```  
>> - (+(bp::char_ - ':') >> "://")  
```  
solved the problem, i guess ">" has a higher priority than "-". closing this for now.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-11-24 19:38:35 UTC  
> Url: https://github.com/boostorg/parser/issues/205#issuecomment-2496197306  

This needs to be called out in the docs so this doesn't happen to more people.
