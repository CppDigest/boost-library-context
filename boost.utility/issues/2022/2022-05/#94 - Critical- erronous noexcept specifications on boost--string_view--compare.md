# #94 - Critical: erronous noexcept specifications on boost::string_view::compare [Closed]

> Username: sehe  
> Created at: 2022-05-02 05:32:37 UTC  
> Updated at: 2022-05-02 21:49:18 UTC  
> Closed at: 2022-05-02 21:49:18 UTC  
> Url: https://github.com/boostorg/utility/issues/94  

Some compare overloads are specified to propagate the `std::out_of_range` exceptions from an inner `substr()` call.  
  
However these `compare` overloads have erroneously been marked as `noexcept` causing programs to abnormally terminated instead.  
  
Reproducer: https://godbolt.org/z/xcM7TsTvj  
  
```c++  
#include <boost/utility/string_view.hpp>  
#include <iostream>  
  
int main() {  
    try {  
        boost::string_view("123").compare(5, 0, boost::string_view{});  
    } catch (std::out_of_range const& oor) {  
        std::cout << "not caught - terminated instead\n";  
    }  
}  
```
