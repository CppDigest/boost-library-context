# #235 - to_chars general format doesn't produce shortest output in some cases [Open]

> Username: ergpudb  
> Created at: 2024-11-30 19:56:07 UTC  
> Updated at: 2024-12-06 14:29:48 UTC  
> Url: https://github.com/boostorg/charconv/issues/235  

Using to_chars with general format seems to not produce the shortest output in some simple cases.  
  
For example, comparing boost charconv, std charconv (gcc 14), and fmt:  
  
```  
#include <iostream>  
#include <charconv>  
#include <boost/charconv.hpp>  
#include <fmt/format.h>  
  
int main()  
{  
    double v = 0.1;  
  
    char buffer1[64] {};  
    auto r1 = boost::charconv::to_chars(buffer1, buffer1 + sizeof(buffer1), v, boost::charconv::chars_format::general);  
    std::cout << buffer1 << "\n";  
  
    char buffer2[64] {};  
    auto r2 = std::to_chars(buffer2, buffer2 + sizeof(buffer2), v, std::chars_format::general);  
    std::cout << buffer2 << "\n";  
  
    char buffer3[64] {};      
    fmt::format_to(buffer3, "{}", v);  
    std::cout << buffer3 << "\n";  
}  
```  
  
Output:  
  
```  
1e-01  
0.1  
0.1  
```  
  
boost is selecting scientific instead of fixed here, even though fixed is shorter.  
  
It seems like it may be an issue with values < 1, since 0.1234 and 0.12345678 also select the longer scientific format, but 1.1, 1.1234, and 1.12345678 select fixed as expected.

---

## Comment 1

> Username: mborland  
> Created at: 2024-12-03 14:49:17 UTC  
> Url: https://github.com/boostorg/charconv/issues/235#issuecomment-2514783889  

This was discussed at the top of #166 (and the links to FMT and MSVC). The situation was improved earlier this year, but not completely addressed. I'll relook this in a bit.

---

## Comment 2

> Username: jk-jeon  
> Created at: 2024-12-05 22:01:55 UTC  
> Updated at: 2024-12-05 22:03:53 UTC  
> Url: https://github.com/boostorg/charconv/issues/235#issuecomment-2521559482  

0.1 is indeed the right output for the example you brought, but according to my understanding of the spec, `chars_format::general` is *not* supposed to output the shortest output. Printing the shortest output is the behavior *without* the format argument, which is supposed to be a separate overload. `chars_format::general` is supposed to choose between fixed vs scientific based on the decimal exponent, which has little to do with being the shortest. For instance, `0.001` must be printed as such in `chars_format::general`, but `1e-3` is shorter. That is,  
  
```  
to_chars(first, last, 0.001)  
```  
  
must print `1e-3` while  
  
```  
to_chars(first, last, 0.001, chars_format::general)  
```  
  
must print `0.001`.

---

## Comment 3

> Username: ergpudb  
> Created at: 2024-12-06 14:29:47 UTC  
> Url: https://github.com/boostorg/charconv/issues/235#issuecomment-2523373105  

Interesting... playing around with it that does indeed seem to be the case with gcc's std::to_chars (although due to the two-digit exponent gcc at least switches over at 0.0001 instead of 0.001 with no format specified).  
  
Would be good if boost supported both the general and shortest cases, but I guess at least the general case would ideally match.
