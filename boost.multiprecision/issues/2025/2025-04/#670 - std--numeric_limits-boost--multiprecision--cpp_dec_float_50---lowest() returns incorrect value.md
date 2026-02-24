# #670 - std::numeric_limits<boost::multiprecision::cpp_dec_float_50>::lowest() returns incorrect value [Closed]

> Username: kyku  
> Created at: 2025-04-03 18:40:38 UTC  
> Updated at: 2025-04-05 16:59:33 UTC  
> Closed at: 2025-04-05 16:01:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/670  

When debugging some program it struck me that std::numeric_limits::lowest() function returns zero for decimal floats.  
  
The issue can be reduced to  the following program, where I'm comparing the output to the output for a standard type (float):  
  
```cpp  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <iostream>  
#include <limits>  
  
int main() {  
  std::cout << "float: " << std::numeric_limits<float>::lowest() << '\n';  
  std::cout << "cpp_dec_float_50: "  
      << std::numeric_limits<boost::multiprecision::cpp_dec_float_50>::lowest()  
      << '\n';  
}  
```  
This program outputs:  
```  
float: -3.40282e+38  
cpp_dec_float_50: 0  
```  
  
According to this [resource](https://en.cppreference.com/w/cpp/types/numeric_limits/lowest) lowest() is defined to:  
> Returns the lowest finite value representable by the numeric type T, that is, a finite value x such that there is no other finite value y where y < x  
  
For sure, one can store the value of -1 which is less than what lowest() returns, which breaks the definition.

---

## Comment 1

> Username: mborland  
> Created at: 2025-04-04 13:52:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/670#issuecomment-2778795939  

@ckormanyos is there a historical reason this is 0 instead of -std::numeric_limits::max() in the fixed precision cases?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-04-04 14:01:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/670#issuecomment-2778819446  

> a historical reason this is 0 instead of -std::numeric_limits::max() in the fixed precision cases  
  
Hi @mborland and hi @kyku for bringing this into attention.  
  
To me it simply seems like a historical mistake that had never been discovered until this issue.  
  
Cc: @jzmaddock

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-04-04 14:07:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/670#issuecomment-2778833163  

In the `cpp_dec_float` code (if memory serves), there is a preloaded constant that provides the right number for `lowest`. I can try for a fix and see if any tests in CI are tuned to the seemingly wrong answer of zero.  
  
Please allow me to try for this fix in the upcoming days.  
  
Cc: @mborland and @jzmaddock

---

## Comment 4

> Username: mborland  
> Created at: 2025-04-04 14:19:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/670#issuecomment-2778865398  

Thanks @ckormanyos

---

## Comment 5

> Username: ckormanyos  
> Created at: 2025-04-05 16:59:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/670#issuecomment-2780960665  

> std::numeric_limits::lowest() function returns zero for decimal floats  
  
I modified the cod3e to return `-(max)()` for the value of `lowest`, which is the same as the behavior of `cpp_dec_float`. So this is now certainly better than returning zero for the lowest value.  
  
See also #671  
  
Thanks again @kyku for this good find.  
  
Cc: @mborland and @jzmaddock
