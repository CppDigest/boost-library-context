# #23 - BOOST_CURRENT_FUNCTION isn't a string literal [Closed]

> Username: dsh0005  
> Created at: 2021-06-16 21:07:43 UTC  
> Updated at: 2022-02-04 04:08:28 UTC  
> Closed at: 2022-02-04 04:08:28 UTC  
> Url: https://github.com/boostorg/assert/issues/23  

The documentation for `BOOST_CURRENT_FUNCTION` says:  
  
> `BOOST_CURRENT_FUNCTION` expands to a string literal [...]  
  
Under GCC (among others), it gets defined to `__PRETTY_FUNCTION__`, which is not a string literal.  
  
[From the GCC documentation:](https://gcc.gnu.org/onlinedocs/gcc/Function-Names.html)  
  
> These identifiers are variables, not preprocessor macros, and may not be used to initialize `char` arrays or be concatenated with string literals.  
  
Here's a minimal example program:  
```C++  
#include <boost/current_function.hpp>  
  
int main(){  
    static const char * str1 = "in function " "main";  
    static const char * str2 = "in function " BOOST_CURRENT_FUNCTION;  
}  
```  
  
Since most major compilers don't provide a macro that expands to a string literal with the name of the current function, let alone the signature, I suggest changing the documentation to reflect the type of `BOOST_CURRENT_FUNCTION`. [(MSVC is the major exception, it seems)](https://godbolt.org/z/17P9qYMvP)
