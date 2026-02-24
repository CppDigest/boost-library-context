# #171 - Why swapping subparser around >> will stop the program from compiling? [Closed]

> Username: liubing  
> Created at: 2024-04-29 13:49:03 UTC  
> Updated at: 2024-04-30 14:40:33 UTC  
> Closed at: 2024-04-30 14:40:32 UTC  
> Url: https://github.com/boostorg/parser/issues/171  

This program fails to compile on both MSVC & GCC under C++20 mode:  
```  
#include <boost/parser/parser.hpp>  
#include <string>  
  
namespace bp = boost::parser;  
  
struct url_foo_attr  
{  
    char zero_or_one_;  
    std::string digits_;  
};  
  
bp::rule<struct url_foo_tag, url_foo_attr> url_foo = "url_foo";  
auto url_foo_def = bp::lit("/foo/") >> bp::char_('0', '1') >> '/' >> *bp::digit;  
BOOST_PARSER_DEFINE_RULES(url_foo);  
  
int main()  
{  
    bp::parse("/foo/0/1", url_foo);  
    return 0;  
}  
```  
But if you swap the members inside the struct and adjust `url_foo_def ` accordingly, it suddenly works correctly:  
```  
#include <boost/parser/parser.hpp>  
#include <string>  
  
namespace bp = boost::parser;  
  
struct url_foo_attr  
{  
    std::string digits_;  
    char zero_or_one_;  
};  
  
bp::rule<struct url_foo_tag, url_foo_attr> url_foo = "url_foo";  
auto url_foo_def = bp::lit("/foo/") >> *bp::digit >> '/' >> bp::char_('0', '1');  
BOOST_PARSER_DEFINE_RULES(url_foo);  
  
int main()  
{  
    bp::parse("/foo/0/1", url_foo);  
    return 0;  
}  
```  
Why?

---

## Comment 1

> Username: d5ch4k  
> Created at: 2024-04-30 11:18:53 UTC  
> Url: https://github.com/boostorg/parser/issues/171#issuecomment-2085027768  

My Observations:  
Both rules return a `std::optional<std::string>`.  
You can verify it by using the following code with the help of a debugger:  
```  
#include <boost/parser/parser.hpp>  
#include <string>  
  
namespace bp = boost::parser;  
  
auto url_foo_def1 = bp::lit("/foo/") >> bp::char_('0', '1') >> '/' >> *bp::digit;  
auto url_foo_def2= bp::lit("/foo/") >> *bp::digit >> bp::lit("/") >> bp::char_('0', '1');  
  
int main()  
{  
    auto result1 = bp::parse("/foo/0/1", url_foo_def1);  
    if (result1) {  
        std::cout << "success 1" << std::endl;;  
        std::cout << *result1 << std::endl;;  
    } else {  
        std::cout << "error 1";  
    }  
    auto result2 = bp::parse("/foo/0/1", url_foo_def2);  
    if (result2) {  
        std::cout << "success 2" << std::endl;;  
        std::cout << *result2 << std::endl;;  
    } else {  
        std::cout << "error 2";  
    }  
    return 0;  
}  
```  
  
the output is:  
```  
success 1  
01  
success 2  
01  
```  
If you examine the output of your second version by adding an output:  
```  
#include <boost/parser/parser.hpp>  
#include <string>  
  
namespace bp = boost::parser;  
  
struct url_foo_attr  
{  
    std::string digits_;  
    char zero_or_one_;  
};  
  
bp::rule<struct url_foo_tag, url_foo_attr> url_foo = "url_foo";  
auto url_foo_def = bp::lit("/foo/") >> *bp::digit >> '/' >> bp::char_('0', '1');  
BOOST_PARSER_DEFINE_RULES(url_foo);  
  
int main()  
{  
    auto result2 = bp::parse("/foo/0/1", url_foo);  
    if (result2) {  
        std::cout << "success!" << std::endl;  
        std::cout << "digits: '" << result2->digits_ << "' expected '0'" << std::endl;  
        std::cout << "zero_or_one: '" << result2->zero_or_one_ << "' expected '1'"  << std::endl;  
    } else {  
        std::cout << "failed!";  
    }  
  
    return 0;  
}  
```  
the corresponding output is:  
```success!  
digits: '01' expected '0'  
zero_or_one: ' ' expected '1'  
```  
this means it compiled, because your second structure begins with a `std::string` as c++20 seems to allow a partial initialization list but the result is not, what you expected (in c++17 both version do not compile which indicates that the parser result and the structure do not match).  
For forcing a separation you can place `bp::separate[...]` around your rule  
  
Then your first example will compile and work as expected:  
```  
#include <boost/parser/parser.hpp>  
#include <string>  
  
namespace bp = boost::parser;  
  
struct url_foo_attr  
{  
    char zero_or_one_;  
    std::string digits_;  
};  
  
bp::rule<struct url_foo_tag, url_foo_attr> url_foo = "url_foo";  
auto url_foo_def = bp::lit("/foo/") >> bp::separate[bp::char_('0', '1') >> '/' >> *bp::digit];  
BOOST_PARSER_DEFINE_RULES(url_foo);  
  
int main()  
{  
    auto result1 = bp::parse("/foo/0/1", url_foo);  
    if (result1) {  
        std::cout << "success!" << std::endl;  
        std::cout << "zero_or_one: '" << result1->zero_or_one_ << "' expected '0'"  << std::endl;  
        std::cout << "digits: '" << result1->digits_ << "' expected '1'" << std::endl;  
    } else {  
        std::cout << "failed!";  
    }  
    return 0;  
}  
```  
gives    
```  
success!  
zero_or_one: '0' expected '0'  
digits: '1' expected '1'  
  
Process finished with exit code 0  
```  
  
regards  
Martin

---

## Comment 2

> Username: liubing  
> Created at: 2024-04-30 11:25:03 UTC  
> Url: https://github.com/boostorg/parser/issues/171#issuecomment-2085037934  

Got it, thank you very much!
