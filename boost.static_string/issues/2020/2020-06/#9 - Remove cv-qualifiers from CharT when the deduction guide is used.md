# #9 - Remove cv-qualifiers from CharT when the deduction guide is used [Closed]

> Username: sdkrystian  
> Created at: 2020-06-05 19:07:22 UTC  
> Updated at: 2020-07-01 04:16:28 UTC  
> Closed at: 2020-07-01 04:16:28 UTC  
> Url: https://github.com/boostorg/static_string/issues/9  

https://github.com/boostorg/static_string/blob/develop/include/boost/static_string/static_string.hpp#L5849  
  
Primary use-case here is a placeholder type as a NTTP for string literal arguments. The template parameter object will be `const` either way, but we copy from source string so we should strip cv-quals to prevent any suprises.
