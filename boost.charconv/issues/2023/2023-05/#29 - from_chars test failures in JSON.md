# #29 - from_chars test failures in JSON [Closed]

> Username: grisumbras  
> Created at: 2023-05-11 14:22:07 UTC  
> Updated at: 2023-05-12 11:43:11 UTC  
> Closed at: 2023-05-12 11:43:11 UTC  
> Url: https://github.com/boostorg/charconv/issues/29  

I'm experimenting with using this library in Boost.JSON. I get these errors from `from_chars`:  
  
```  
Invalid argument [generic:22 at ../../boost/json/basic_parser_impl.hpp:2691 in function 'parse_number'] after 3 characters of 0E0  
Invalid argument [generic:22 at ../../boost/json/basic_parser_impl.hpp:2691 in function 'parse_number'] after 4 characters of 0E01  
Invalid argument [generic:22 at ../../boost/json/basic_parser_impl.hpp:2691 in function 'parse_number'] after 5 characters of 0.0e0  
Invalid argument [generic:22 at ../../boost/json/basic_parser_impl.hpp:2691 in function 'parse_number'] after 4 characters of -0E0  
Invalid argument [generic:22 at ../../boost/json/basic_parser_impl.hpp:2691 in function 'parse_number'] after 5 characters of -0E01  
Invalid argument [generic:22 at ../../boost/json/basic_parser_impl.hpp:2691 in function 'parse_number'] after 6 characters of -0.0e0  
```
