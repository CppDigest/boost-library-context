# #45 - clang -fsanitize=undefined,integer error [Closed]

> Username: jzmaddock  
> Created at: 2021-03-31 07:51:43 UTC  
> Updated at: 2024-02-15 06:24:46 UTC  
> Closed at: 2024-02-15 06:24:45 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/45  

When parsing a negative integer, clang triggers an USAN error, test case is simply:  
```  
  
#include <boost/lexical_cast.hpp>  
  
  
int main()  
{  
   return boost::lexical_cast<int>("-6575543");  
}  
```  
Compile with `clang++ -fsanitize=undefined,integer` and you get:  
  
```boost/lexical_cast/detail/converter_lexical_streams.hpp:554:51: runtime error: unsigned integer overflow: 0 - 6575543 cannot be represented in type 'unsigned int'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior boost/lexical_cast/detail/converter_lexical_streams.hpp:554:51 in   
```  
This one looks like a real error to me - the code is subtracting two unsigned values, the first of which is zero.  A better solution might be to assign the unsigned value to the signed result and then negate, though this would have to account for the INT_MIN special case.  
  
Found while investigating https://github.com/boostorg/multiprecision/issues/313

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-08 23:24:18 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/45#issuecomment-1033162411  

Subtracting unsigned values is well defined. -fsanitize=integer diagnoses unsigned integer overflows, which is not undefined behavior per standard. Unsigned integers use modular arithmetic.

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-02-13 08:45:10 UTC  
> Updated at: 2024-02-13 08:45:27 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/45#issuecomment-1940775723  

Double checked the issue. The docs for the sanitizer option say that it is not an undefined behavior, and the sanitizer option is meant to draw attention to a place with potential issue.  
  
Fortunately there is a way to tell sanitizer "yes, I know that therecs no issue here" via `__attribute__((no_sanitize("unsigned-integer-overflow"))) `. I'll add those workarounds soon
