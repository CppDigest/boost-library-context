# #139 - Improve locale logic [Closed]

> Username: mborland  
> Created at: 2024-01-25 08:58:44 UTC  
> Updated at: 2024-01-25 09:52:50 UTC  
> Closed at: 2024-01-25 09:52:50 UTC  
> Url: https://github.com/boostorg/charconv/issues/139  

Locales are used in some corner cases along with switching them back and  
forth  
https://github.com/cppalliance/charconv/blob/41a83261c0f65aee575e89ccb71d94b9ddcf799b/include/boost/charconv/detail/from_chars_float_impl.hpp#L64  
  
Looks like there's no need in that line for locales switching. Just get the  
decimal point separator from the current locale and if it is not '.' then  
do the replacement of the first '.' to the locale's separator.
