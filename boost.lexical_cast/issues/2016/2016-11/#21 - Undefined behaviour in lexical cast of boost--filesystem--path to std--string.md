# #21 - Undefined behaviour in lexical cast of boost::filesystem::path to std::string [Closed]

> Username: oxidase  
> Created at: 2016-11-15 22:12:02 UTC  
> Updated at: 2016-12-04 12:54:00 UTC  
> Closed at: 2016-12-04 12:54:00 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/21  

Lexical cast of boost::filesystem::path to std::string as in  
```  
#include <iostream>  
#include <boost/filesystem.hpp>  
#include <boost/lexical_cast.hpp>  
  
int main() {  
    boost::filesystem::path p("hello");  
    std::cout << boost::lexical_cast<std::string>(p) << "\n";  
}  
```  
with `-fsanitize=undefined` for gcc 6.2.0 brings the following usan message  
```  
/usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:235:43: runtime error: downcast of address 0x7ffc173273a8 which does not point to an object of type 'basic_unlockedbuf'  
0x7ffc173273a8: note: object is of type 'std::__cxx11::basic_stringbuf<char, std::char_traits<char>, std::allocator<char> >'  
 c1 7f 00 00  e8 da 6e af c1 7f 00 00  21 ec b1 f9 71 55 00 00  21 ec b1 f9 71 55 00 00  21 ec b1 f9  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'std::__cxx11::basic_stringbuf<char, std::char_traits<char>, std::allocator<char> >'  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-12-04 12:54:00 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/21#issuecomment-264702308  

Fixed in cf5b7a7, will be in Boost 1.63
