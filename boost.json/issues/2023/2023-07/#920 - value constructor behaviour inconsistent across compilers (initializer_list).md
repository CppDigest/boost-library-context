# #920 - value constructor behaviour inconsistent across compilers (initializer_list) [Closed]

> Username: sehe  
> Created at: 2023-07-19 22:45:07 UTC  
> Updated at: 2023-08-25 17:54:50 UTC  
> Closed at: 2023-08-25 17:54:50 UTC  
> Url: https://github.com/boostorg/json/issues/920  

- Boost 1.82.0  
  
Repro:  
  
```c++  
#include <boost/json/src.hpp>  
namespace json = boost::json;  
  
int main() {  
    json::value a, b{a};  
    assert(b.is_array()); // GCC pass, Clang fail  
}  
```  
  
Compare results [On Compiler Explorer](https://godbolt.org/z/oYv5KKvsT)  
  
I'm not qualified to know which compiler is in error (I'm tempted to side with GCC, but I'm not a language-lawyer), but no doubt someone else knows. (Related context: https://stackoverflow.com/q/76724646/85371)
