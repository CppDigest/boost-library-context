# #119 - variadic min/max/minmax [Open]

> Username: gpeterhoff  
> Created at: 2023-06-24 13:37:32 UTC  
> Updated at: 2023-06-27 01:42:03 UTC  
> Url: https://github.com/boostorg/algorithm/issues/119  

Hello,  
for min/max/minmax with multiple parameters there are only functions per std::initializer_list:  
https://en.cppreference.com/w/cpp/algorithm/min  
https://en.cppreference.com/w/cpp/algorithm/max  
https://en.cppreference.com/w/cpp/algorithm/minmax  
The parameters are always passed by value and not by reference and the result is also a value. This is a problem with types like std::array, std::string, etc.  
  
Since the compare class cannot be the last function parameter in a variadic implementation (as in the std implementations) another position must be found, possibilities:  
- first function parameter: 2 implementations would be needed each, once with compare class and once without. I don't find that useful.  
- first template parameter: only one implementation is needed with default template parameter(s).  
  
A simple variadic implementation could look like this:  
[minmax_variadic.hpp.txt](https://github.com/boostorg/algorithm/files/11857077/minmax_variadic.hpp.txt)  
  
minmax needs 2 compare classes (each for min and max) - this is more flexible.  
  
thx  
Gero

---

## Comment 1

> Username: mclow  
> Created at: 2023-06-27 01:41:25 UTC  
> Updated at: 2023-06-27 01:42:03 UTC  
> Url: https://github.com/boostorg/algorithm/issues/119#issuecomment-1608577073  

There is also [`std::minmax_element`](https://en.cppreference.com/w/cpp/algorithm/minmax_element), which takes a sequence.
