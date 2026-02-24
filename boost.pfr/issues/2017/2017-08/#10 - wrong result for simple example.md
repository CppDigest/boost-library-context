# #10 - wrong result for simple example [Closed]

> Username: onqtam  
> Created at: 2017-08-22 15:34:51 UTC  
> Updated at: 2020-08-20 16:10:16 UTC  
> Closed at: 2020-08-20 16:10:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/10  

I used the following code:  
  
```c++  
#include <iostream>  
#include <boost/pfr/precise/tuple_size.hpp>  
  
class Base {  
    char a;  
    int b;  
};  
  
class Derived : Base {  
    std::string c;  
};  
  
int main() {  
    std::cout << boost::pfr::tuple_size<Base>::value << std::endl;  
    std::cout << boost::pfr::tuple_size<Derived>::value << std::endl;  
}  
```  
  
and I got this as output:  
  
```  
1  
1  
```  
  
shouldn't it be 2 and 1?  
  
I'm using these 2 compilers:  
  
- g++ (x86_64-posix-seh-rev0, Built by MinGW-W64 project) 7.1.0 (under Windows)  
- Microsoft Visual Studio Community 2017 - Version 15.3.1 - VisualStudio.15.Release/15.3.1+26730.8

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-08-22 19:51:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/10#issuecomment-324133120  

`class Base` has all it's members private and violates the requirements of the library: http://apolukhin.github.io/magic_get/boost_precise_and_flat_reflectio/requirements_and_limitations.html (`Base` is **not aggregate initializable**). With C++17 you will get a compile time error, while in C++14 there's no known way to assert that case.  
  
Same story with `Derived` - all members are private.  
  
If there's some unknown to me trick to detect "all fields are private" case - please give me a hint.

---

## Comment 2

> Username: onqtam  
> Created at: 2017-08-23 10:44:35 UTC  
> Updated at: 2017-08-23 10:48:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/10#issuecomment-324291997  

Oh - I completely forgot about the accessibility of class vs struct.  
  
This might sound dumb/naive, but do you think there is a way to make it work for a type with private fields by friend-ing some trait - perhaps like this:  
  
```c++  
template <std::size_t N, class T>  
friend struct boost::pfr::detail::is_single_field_and_aggregate_initializable;  
template <class T, std::size_t N>  
friend struct boost::pfr::detail::is_aggregate_initializable_n;  
```

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-08-23 16:53:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/10#issuecomment-324396551  

Hm... There's no way to distinguish between private fields and structure with one field. A trait may be the right solution: if you're doing reflection for structure with one fild, specialize the "I know what I'm doing" trait.  
  
I need to experiment with that. Thanks for the idea!

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-08-20 16:10:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/10#issuecomment-677758689  

I've done some thinking :)  
  
Private fields and friends seems to be an exotic use case.   
  
I've updated the docs and now C++17 is recommended (C++14 is supported). With C++17 the initial issue is well diagnosed. Nothing to improve for C++14
