# #50 - Compilation fails when source is a volatile integer type and output is the same non-volatile type [Closed]

> Username: gcerretani  
> Created at: 2022-05-18 08:50:32 UTC  
> Updated at: 2022-09-01 12:34:11 UTC  
> Closed at: 2022-09-01 10:54:44 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/50  

This code fails to compile at least since Boost 1.64  
  
https://godbolt.org/z/Mfr4KPKbx  
  
When source is nteger type, the failure arises when target is the same non-volatile type, without volatile. Only `unsigned char` works, but this seems reasonable since it is treated as pure `char` internally.  
  
https://godbolt.org/z/dnKbWE99q  
  
```c++  
#include <boost/lexical_cast.hpp>  
  
template <typename Int>  
void foo() {  
    volatile Int v{};  
    boost::lexical_cast<Int>(v);  
}  
  
void test() {  
  
    foo<signed char>();  
    foo<signed int>(); // fails  
    foo<signed short>(); // fails  
    foo<signed long>(); // fails  
    foo<signed long long>(); // fails  
  
    foo<unsigned char>();  
    foo<unsigned int>(); // fails  
    foo<unsigned short>(); // fails  
    foo<unsigned long>(); // fails  
    foo<unsigned long long>(); // fails  
  
}  
```

---

## Comment 1

> Username: gcerretani  
> Created at: 2022-05-18 08:56:27 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/50#issuecomment-1129750763  

Some more info. When output type is not the same of non-volatile source, then the code compiles:  
  
```c++  
#include <boost/lexical_cast.hpp>  
  
template <typename Int>  
void foo() {  
  
    volatile Int xv{};  
  
    boost::lexical_cast<signed char>(xv);  
    boost::lexical_cast<unsigned char>(xv);  
  
    boost::lexical_cast<signed short>(xv);  
    boost::lexical_cast<unsigned short>(xv);  
  
    boost::lexical_cast<signed int>(xv);  
    boost::lexical_cast<unsigned int>(xv); // only this is broken when Int is unsigned int  
  
    boost::lexical_cast<signed long>(xv);  
    boost::lexical_cast<unsigned long>(xv);  
  
    boost::lexical_cast<signed long long>(xv);  
    boost::lexical_cast<unsigned long long>(xv);  
  
}  
  
void test() {  
    foo<unsigned int>();  
}  
```

---

## Comment 2

> Username: apolukhin  
> Created at: 2022-09-01 10:54:56 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/50#issuecomment-1234106067  

Thanks for the bug report!
