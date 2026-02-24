# #19 - switch statement case label has only `int` template argument [Open]

> Username: schaumb  
> Created at: 2020-07-27 10:06:26 UTC  
> Updated at: 2020-07-27 10:06:26 UTC  
> Url: https://github.com/boostorg/lambda/issues/19  

Before gcc9, if a template argument is an integral value, no compiler error/warning generated when the specified value is bigger than the integral value MAX.   
  
This leads the following runtime issue:  
```  
#include <iostream>  
  
#include <boost/lambda/lambda.hpp>  
#include <boost/lambda/switch.hpp>  
  
int main() {  
    using namespace boost;  
    using namespace boost::lambda;  
      
    switch_statement(  
        _1,  
        case_statement<2147483648>(std::cout << constant("big")),  
        default_statement(std::cout << constant("other"))  
    )(2147483648); // prints 'other'  
    // static_cast<int>(2147483648) is OK  
}  
```  
  
This issue prone working can be eliminated in any following way:  
- replace case_statement template argument from `int` to `uintmax_t` or `intmax_t` or any bigger than int. This solves that not only `int` parameters can be accepted in later gcc's and clang too.   
- switch_statement lambda accepts/casts argument `int`. This solves type safety.  
- some other template magic, where at case statement can be set the acceptable integral type, and switch statement recognizes, and uses `common_type` to casts lambda parameter to that.
