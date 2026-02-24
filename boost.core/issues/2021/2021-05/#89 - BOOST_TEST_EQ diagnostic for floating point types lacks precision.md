# #89 - BOOST_TEST_EQ diagnostic for floating point types lacks precision [Open]

> Username: Kojoley  
> Created at: 2021-05-15 15:37:16 UTC  
> Updated at: 2021-05-15 16:04:15 UTC  
> Url: https://github.com/boostorg/core/issues/89  

```  
#include <boost/core/lightweight_test.hpp>  
#include <limits>  
  
int main()  
{  
    BOOST_TEST_EQ(0x9.d495182a99318p-13, 0x9.d495182a99308p-13);  
    std::cerr.precision(std::numeric_limits<double>::max_digits10);  
    std::cerr << 0x9.d495182a99318p-13 << " == " << 0x9.d495182a99308p-13 << '\n';  
    return boost::report_errors();  
}  
```  
```  
prog.cc(5): test '0x9.d495182a99318p-13 == 0x9.d495182a99308p-13' ('0.0012' == '0.0012') failed in function 'int main()'  
0.0012000000000000003 == 0.0011999999999999999  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2021-05-15 15:46:12 UTC  
> Url: https://github.com/boostorg/core/issues/89#issuecomment-841682287  

```  
#include <boost/core/lightweight_test.hpp>  
#include <limits>  
  
int main()  
{  
    BOOST_LIGHTWEIGHT_TEST_OSTREAM.precision(std::numeric_limits<double>::max_digits10);  
    BOOST_TEST_EQ(0x9.d495182a99318p-13, 0x9.d495182a99308p-13);  
    return boost::report_errors();  
}  
```  
  
```  
prog.cc(7): test '0x9.d495182a99318p-13 == 0x9.d495182a99308p-13' ('0.0012000000000000003' == '0.0011999999999999999') failed in function 'int main()'  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2021-05-15 16:03:30 UTC  
> Url: https://github.com/boostorg/core/issues/89#issuecomment-841684762  

It probably makes sense to set the precision before printing floating point, but we need to restore it afterwards, and digits10 is C++11, so it's a bit of a hassle. Although maybe not so much.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-05-15 16:04:15 UTC  
> Url: https://github.com/boostorg/core/issues/89#issuecomment-841684899  

Ah wait, it's not C++11. :-)
