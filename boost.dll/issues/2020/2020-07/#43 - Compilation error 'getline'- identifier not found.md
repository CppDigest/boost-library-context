# #43 - Compilation error 'getline': identifier not found [Closed]

> Username: slay-er  
> Created at: 2020-07-21 14:23:00 UTC  
> Updated at: 2020-07-23 12:28:49 UTC  
> Closed at: 2020-07-23 12:28:49 UTC  
> Url: https://github.com/boostorg/dll/issues/43  

Hi,  
  
on Windows I get the following compilation error.  
  
include\boost\dll\detail\pe_info.hpp(291,13): error C3861: 'getline': identifier not found  
include\boost\dll\detail\pe_info.hpp(291,13): error C3861:             getline(fs, symbol_name, '\0');  
include\boost\dll\detail\pe_info.hpp(291,13): error C3861:             ^  
  
When I change the include of **cstring** to **string** it works.  
  
According to cppreference std::getline is defined in header _string_.  
https://en.cppreference.com/w/cpp/string/basic_string/getline

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-07-23 12:28:46 UTC  
> Url: https://github.com/boostorg/dll/issues/43#issuecomment-662978443  

Many thanks for the report!
