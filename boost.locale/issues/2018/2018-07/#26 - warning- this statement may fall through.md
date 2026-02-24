# #26 - warning: this statement may fall through [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 07:39:31 UTC  
> Updated at: 2022-09-17 20:58:27 UTC  
> Closed at: 2022-09-17 20:58:27 UTC  
> Url: https://github.com/boostorg/locale/issues/26  

This program:  
```  
#include <iostream>  
#include <string>  
#include <boost/locale/utf.hpp>  
int main() {  
  std::string s{"a"};  
  auto begin = &s[0];  
  auto end = begin + s.size();  
  boost::locale::utf::code_point c =  
      boost::locale::utf::utf_traits<char>::decode(begin, end);  
  std::cout << "codepoint = " << c << '\n';  
  return 0;  
}  
```  
compiled on linux x86_64 with g++ 8.2.0 (same with 7.3.0) and boost 1.68.0 beta 1 with the following command:  
`g++-8.2.0 -o locale.o -c -Wall -Wextra -std=c++14 -I/softs/lin64-gcc-8.2.0/debug/boost/include locale.cpp`  
gives the following warnings:  
```  
In file included from locale.cpp:3:  
/softs/lin64-gcc-8.2.0/debug/boost/include/boost/locale/utf.hpp: In static member function 'static boost::locale::utf::code_point boost::locale::utf::utf_traits<CharType, 1>::decode(Iterator&, Iterator) [with Iterator = char*; CharType = char]':  
/softs/lin64-gcc-8.2.0/debug/boost/include/boost/locale/utf.hpp:224:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
                 c = (c << 6) | ( tmp & 0x3F);  
                 ^  
/softs/lin64-gcc-8.2.0/debug/boost/include/boost/locale/utf.hpp:225:13: note: here  
             case 2:  
             ^~~~  
/softs/lin64-gcc-8.2.0/debug/boost/include/boost/locale/utf.hpp:231:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
                 c = (c << 6) | ( tmp & 0x3F);  
                 ^  
/softs/lin64-gcc-8.2.0/debug/boost/include/boost/locale/utf.hpp:232:13: note: here  
             case 1:  
             ^~~~  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-09-17 20:58:27 UTC  
> Url: https://github.com/boostorg/locale/issues/26#issuecomment-1250139527  

Fixed by #41
