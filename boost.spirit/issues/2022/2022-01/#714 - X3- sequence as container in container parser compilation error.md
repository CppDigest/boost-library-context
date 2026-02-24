# #714 - X3: sequence as container in container parser compilation error [Open]

> Username: Bockeman  
> Created at: 2022-01-21 15:20:32 UTC  
> Updated at: 2025-05-09 23:41:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/714  

MCVE:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <vector>  
int main()  
{  
    using namespace boost::spirit::x3;  
    char const* const s = "ab12", * const e = s + std::strlen(s);  
    std::vector<std::vector<char>> v;  
    parse(s, e, +(char_ >> char_), v);  
}  
```  
  
Workaround by using an intermediate rule:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <vector>  
int main()  
{  
    using namespace boost::spirit::x3;  
    char const* const s = "ab12", * const e = s + std::strlen(s);  
    std::vector<std::vector<char>> v;  
    parse(s, e, +(rule<class BOOST_JOIN(rule_id_, __LINE__), std::vector<char>>{} = char_ >> char_), v);  
}  
```
