# #29 - Reading from stream strange handling of extra characters [Open]

> Username: mglisse  
> Created at: 2017-06-16 12:43:04 UTC  
> Updated at: 2019-06-30 10:43:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/29  

```c++  
#include <boost/multiprecision/cpp_int.hpp>  
#include <iostream>  
  
typedef boost::multiprecision::cpp_int I;  
  
int main(){  
  I i;  
  std::string s;  
  std::istringstream is("12+3");  
  is >> i >> s;  
  std::cout << i << '\n';  
  std::cout << s << '\n';  
}  
```  
  
causes with boost 1.62 (or with a fresh clone):  
  
> terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::runtime_error> >'  
  what():  Unexpected character encountered in input.  
  
However, using `int` instead of `I`, I get  
12  
+3  
as expected.  
The string "12w3" gives  
12  
w3  
with `int` or `I`, so it looks like the parser in Boost.Multiprecision is confused because `+` can be part of a number but not in that position.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-06-30 10:43:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/29#issuecomment-507026042  

Old bug, but confirmed as still present, albeit in a different form....
