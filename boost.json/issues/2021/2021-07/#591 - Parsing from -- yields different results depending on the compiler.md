# #591 - Parsing from "[{}]" yields different results depending on the compiler [Closed]

> Username: hedtke  
> Created at: 2021-07-05 09:50:50 UTC  
> Updated at: 2021-07-05 14:28:25 UTC  
> Closed at: 2021-07-05 14:28:25 UTC  
> Url: https://github.com/boostorg/json/issues/591  

When I try to parse an object inside an array, the structure is recognized using clang. But using GCC it interprets this as an array of array.  
  
### Version of Boost  
  
1.76.0  
  
### Steps necessary to reproduce the problem  
  
The following code yields different results depending on the compiler:  
  
```cpp  
#include <boost/json/src.hpp>  
#include <iostream>  
#include <string>  
  
std::string true_cxx =  
#ifdef __clang__  
        "clang++";  
#else  
        "g++";  
#endif  
  
int main() {  
    std::cout << "Compiler: " << true_cxx << " " << __VERSION__ << std::endl;  
    std::string toParse{"[{}]"};  
    auto parsed{boost::json::parse(toParse)};  
    std::cout << parsed.kind() << std::endl;  
    if (parsed.is_array()) {  
        const auto &asArray{parsed.as_array()};  
        for (const auto &elem : asArray) {  
            std::cout << "\t" << elem.kind() << std::endl;  
        }  
    }  
    return 0;  
}  
```  
  
With clang:  
  
```  
Compiler: clang++ Apple LLVM 12.0.5 (clang-1205.0.22.11)  
array  
	object  
```  
  
With GCC:  
  
```  
Compiler: g++ 9.3.0  
array  
        array  
```  
  
### All relevant compiler information  
  
* clang++ Apple LLVM 12.0.5 (clang-1205.0.22.11) on Mac OS 11.4  
* g++ 9.3.0 on Ubuntu 20.04.2

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-07-05 14:01:29 UTC  
> Url: https://github.com/boostorg/json/issues/591#issuecomment-874136922  

This is a problem with C++ construction using initializer lists. Change the line to:  
```  
boost::json::value parsed = boost::json::parse( toParse );  
```  
  
Unfortunately there's nothing we can do, as it is the compiler's behavior.

---

## Comment 2

> Username: hedtke  
> Created at: 2021-07-05 14:28:25 UTC  
> Url: https://github.com/boostorg/json/issues/591#issuecomment-874156368  

Thank you! That fixes the problem.
