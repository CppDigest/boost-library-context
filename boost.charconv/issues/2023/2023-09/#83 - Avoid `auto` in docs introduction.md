# #83 - Avoid `auto` in docs introduction [Closed]

> Username: akrzemi1  
> Created at: 2023-09-22 06:40:47 UTC  
> Updated at: 2023-09-22 08:27:24 UTC  
> Closed at: 2023-09-22 08:27:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/83  

The initial docs examples use `auto` as i n:  
  
```  
auto r = boost::charconv::from_chars(buffer, buffer + std::strlen(buffer), v);  
```  
  
I recommend spelling out the return type:  
  
```  
boost::charconv::from_chars_result r = boost::charconv::from_chars(buffer, buffer + std::strlen(buffer), v);  
```  
  
It will make it clear for the person that first reads the introduction that the return type is also part of this library, as opposed to using another Boost library or a component from STD.
