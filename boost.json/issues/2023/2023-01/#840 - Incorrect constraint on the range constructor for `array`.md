# #840 - Incorrect constraint on the range constructor for `array` [Closed]

> Username: JankoDedic  
> Created at: 2023-01-14 17:10:42 UTC  
> Updated at: 2023-08-23 16:49:24 UTC  
> Closed at: 2023-08-23 16:49:24 UTC  
> Url: https://github.com/boostorg/json/issues/840  

Range constructor for `array` is [constrained](https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/ref/boost__json__array/array/overload5.html) with:  
  
```cpp  
std::is_constructible_v<value, std::iterator_traits<InputIt>::value_type>  
```  
  
but I believe that should be:  
  
```cpp  
std::is_constructible_v<value, std::iterator_traits<InputIt>::reference>  
```
