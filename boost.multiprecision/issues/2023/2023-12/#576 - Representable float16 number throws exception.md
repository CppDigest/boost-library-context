# #576 - Representable float16 number throws exception [Closed]

> Username: michail-boulasikis  
> Created at: 2023-12-08 15:02:39 UTC  
> Updated at: 2023-12-16 09:21:30 UTC  
> Closed at: 2023-12-16 09:21:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/576  

Hello,  
I am trying to emulate float16 using this library and I have run into a problem. The problem is that I cannot print numbers which are in the range of what float16 can handle, such as 0.05, without getting an exception (I am unsure whether the rest of the functionality is intact). The exception that I get is the following:  
  
```  
terminate called after throwing an instance of 'boost::wrapexcept<std::range_error>'  
  what():  No bits were set in the operand.  
Aborted (core dumped)  
```  
  
I am perplexed as to why, since bfloat16 seems to work just fine. Could it be the small exponent?  
Here is a minimal example to reproduce this behavior.  
  
```  
#include <iostream>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using namespace boost::multiprecision;  
typedef number<backends::cpp_bin_float<11, backends::digit_base_2, void, std::int16_t, -14, 15>, et_off> float16_t;  
typedef number<backends::cpp_bin_float<8, backends::digit_base_2, void, std::int16_t, -126, 127>, et_off> bfloat16_t;  
  
int main(int argc, char *argv[]) {  
    bfloat16_t nice_number{0.05};  
    float16_t problematic_number{0.05};  
    std::cout << nice_number << "\n"; // ok  
    std::cout << problematic_number << "\n"; // throws  
    return 0;  
}  
```  
  
My boost version (as obtained by running `dkpg -l | grep boost`) is 1.74.0. I am in Ubuntu 22.04 and the code was compiled using g++ version 11.4.0. Thank you for your help.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-12-08 17:09:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/576#issuecomment-1847540102  

Reproduced, there's an error in the logic for our IO formatting code somewhere, will investigate.

---

## Comment 2

> Username: michail-boulasikis  
> Created at: 2023-12-09 00:02:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/576#issuecomment-1847988430  

Thanks for the quick reply. Not sure how helpful this is, but the exception occurs in the `operator<<` overload. More specifically, control goes to line 588 in `io.hpp` (function `restricted_multiply`) and then to line 25 (function `msb`) which eventually throws the exception, because the condition `eval_get_sign(a) == 0` is true.  
  
Reading library code is quite difficult for me still. But I will see if I can find anything else. :-)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-12-10 13:12:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/576#issuecomment-1848962244  

I have this narrowed down and a possible fix, but I need to write more tests: as having `MaxExponent < numeric_limits<int>::bits` causes all sorts of problems in our internal logic.
