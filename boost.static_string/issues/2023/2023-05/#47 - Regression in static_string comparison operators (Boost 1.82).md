# #47 - Regression in static_string comparison operators (Boost 1.82) [Closed]

> Username: audaki  
> Created at: 2023-05-29 11:00:57 UTC  
> Updated at: 2023-05-30 09:06:42 UTC  
> Closed at: 2023-05-29 15:28:09 UTC  
> Url: https://github.com/boostorg/static_string/issues/47  

Dear static_string/boost maintainers,  
  
Simple reproduction case for a new compilation error I have since 1.82:  
  
```  
#include <boost/static_string.hpp>  
struct My_uuid: public boost::static_string<32> {  
};  
  
static inline bool comparison_issue() {  
  My_uuid s1, s2;  
  return s1 < s2;  
}  
```  
  
The error:  
  
```  
~/project/article.cpp:11:13: error: use of overloaded operator '<' is ambiguous (with operand types 'My_uuid' and 'My_uuid')  
  return s1 < s2;  
  
~/local/include/boost/static_string/static_string.hpp:5750:1: note: candidate function [with N = 32, CharT = char, Traits = std::char_traits<char>, T = My_uuid, $4 = void]  
operator<(  
  
~/local/include/boost/static_string/static_string.hpp:5768:1: note: candidate function [with N = 32, CharT = char, Traits = std::char_traits<char>, T = My_uuid, $4 = void]  
operator<(  
```  
  
This code worked from Boost 1.74 up until Boost 1.81 (Code is 3 years old).  
  
Compiler is Clang 14 in Ubuntu 22.04 and Boost is/was always compiled and installed from Source.  
  
PS: Thank you for the library!  
  
rgds, Kira

---

## Comment 1

> Username: audaki  
> Created at: 2023-05-30 09:06:42 UTC  
> Url: https://github.com/boostorg/static_string/issues/47#issuecomment-1568062818  

@sdkrystian thank you!
