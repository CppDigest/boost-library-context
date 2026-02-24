# #729 - Unable to include boost in .h file and then use library in .cpp implementation of .h file [Closed]

> Username: g2px1  
> Created at: 2022-07-22 09:14:16 UTC  
> Updated at: 2022-07-29 08:35:40 UTC  
> Closed at: 2022-07-29 08:35:40 UTC  
> Url: https://github.com/boostorg/json/issues/729  

boost 1.79.0  
If we've for example Class.h and Class.cpp and #include "boost/json.hpp" in Class.h we'll get an error with duplicating symbol. Other boost libraries is ok with that configuration.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-07-22 09:27:11 UTC  
> Url: https://github.com/boostorg/json/issues/729#issuecomment-1192371972  

There's not enough information in your description of the issue. What's the OS? What's the compiler? How do you link to the library? Did you follow the documentation on how to use the library?

---

## Comment 2

> Username: g2px1  
> Created at: 2022-07-22 15:21:30 UTC  
> Updated at: 2022-07-22 15:29:59 UTC  
> Url: https://github.com/boostorg/json/issues/729#issuecomment-1192683319  

> There's not enough information in your description of the issue. What's the OS? What's the compiler? How do you link to the library? Did you follow the documentation on how to use the library?  
  
Mac OS Monterey 12.0.1.   
Compiler: Apple clang version 13.1.6 (clang-1316.0.21.2.5)  
C++ 17  
Including like this:  
```c++  
#include "boost/json/src.hpp"  
```  
  
# The simplest example to get an error:  
  
> Class.h  
```c++  
#ifndef TEST_CLASS_H  
#define TEST_CLASS_H  
#include "iostream"  
#include "boost/json/src.hpp"  
#include "boost/json/array.hpp"  
#include "boost/json/object.hpp"  
  
class Class {  
public:  
     inline void test();  
}  
  
#endif //TEST_CLASS_H  
```  
  
> Class.cpp  
```c++  
#include "Class.h"  
  
void Class::test() {  
    std::cout << "Hello, world" << std::endl;  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-07-22 15:48:13 UTC  
> Url: https://github.com/boostorg/json/issues/729#issuecomment-1192709776  

Pay attention to the section in **bold**:  
> To use as header-only; that is, to eliminate the requirement to link a program to a static or dynamic Boost.JSON library, simply place the following line in **exactly one new or existing source file** in your project.  
  
https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only  
  
You are including src.hpp multiple times.

---

## Comment 4

> Username: g2px1  
> Created at: 2022-07-22 17:06:58 UTC  
> Url: https://github.com/boostorg/json/issues/729#issuecomment-1192774386  

> Pay attention to the section in **bold**:  
>   
> > To use as header-only; that is, to eliminate the requirement to link a program to a static or dynamic Boost.JSON library, simply place the following line in **exactly one new or existing source file** in your project.  
>   
> https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only  
>   
> You are including src.hpp multiple times.  
  
Oh, I haven't seen that, sorry then.
