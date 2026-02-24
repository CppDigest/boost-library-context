# #945 - Compiling lexical_cast to an integer from a char with gcc and -O3 results in "array subscript -1 is outside array bounds" warnings [Open]

> Username: a-kryuk  
> Created at: 2024-08-25 20:15:06 UTC  
> Updated at: 2024-08-25 20:15:06 UTC  
> Url: https://github.com/boostorg/boost/issues/945  

Starting with boost 1.85, when compiling the following code ([Godbolt](https://godbolt.org/z/q7c5jK799))  
``` cpp  
#include <boost/lexical_cast.hpp>  
int f(char n) {  
  return boost::lexical_cast<int>(n);  
}  
```  
`gcc -O3 -Wall -Wextra -pedantic` produces scary-looking warnings `array subscript -1 is outside array bounds`.
