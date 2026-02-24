# #257 - boost::regex_match should delete the rvalue reference overload [Open]

> Username: supernun  
> Created at: 2025-07-24 17:01:30 UTC  
> Updated at: 2025-07-24 17:01:30 UTC  
> Url: https://github.com/boostorg/regex/issues/257  

https://github.com/boostorg/regex/blob/develop/include/boost/regex/v5/regex_match.hpp#L65  
  
The const-ref overload gets picked up in this code-snippet:  
  
```cpp  
std::string GetStr();  
  
boost::smatch m;  
if (boost::regex_match(GetStr(), m, boost::regex{"f(o)o"}) {  
    m.str(1);  // dangling reference  
}  
```  
  
Issue sneaks up on you when `GetStr` used to return a `const std::string &` but now returns it by value.  
  
Notably: the std::regex implementation deletes the rvalue reference overload: https://en.cppreference.com/w/cpp/regex/regex_match.html  
  
Boost should probably do the same here.  
  
```cpp  
BOOST_REGEX_MODULE_EXPORT template <class ST, class SA, class Allocator, class charT, class traits>  
inline bool regex_match(std::basic_string<charT, ST, SA>&&,   
                 match_results<typename std::basic_string<charT, ST, SA>::const_iterator, Allocator>& m,   
                 const basic_regex<charT, traits>& e,   
                 match_flag_type flags = match_default) = delete;  
```
