# #527 - Alternative parser does not rollback on failure [Closed]

> Username: iwrinc  
> Created at: 2019-08-25 12:11:27 UTC  
> Updated at: 2019-08-25 12:17:58 UTC  
> Closed at: 2019-08-25 12:17:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/527  

The following code outputs "ac" in 1.70 (outputs "c", as expected, before 1.70).  Is it a bug or breaking change in Spirit?  
  
```  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
using x3::char_;  
using x3::rule;  
using x3::lit;  
  
template <typename T>   
auto as = [](auto p) { return rule<struct _, T> {} = p; };  
  
auto const test =   
	as<std::string>(x3::char_('a') >> 'b') |  as<std::string>(lit('a') >> char_('c'));  
  
  
int main() {  
    std::string input = "ac";  
    std::string output;  
    if (x3::parse(input.begin(), input.end(), test, output)) {  
        std::cout << output;  
    }  
}  
   
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-08-25 12:17:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/527#issuecomment-524625611  

Duplicate of boostorg/spirit#523. Not a bug, see boostorg/spirit#523, boostorg/spirit#835.
