# #563 - msvc compiler warning turned as error are restored to warning [Open]

> Username: gzzi  
> Created at: 2021-09-24 06:49:34 UTC  
> Updated at: 2021-12-06 08:44:20 UTC  
> Url: https://github.com/boostorg/boost/issues/563  

Hello,  
  
We would like to compile code with msvc and warning [C4002](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-1-c4002?view=msvc-160) as error. To do so, we set the compiler flag /we4002.  
  
When we include some boost header, the compiler error 4002 is set back to warning due to [this line](https://github.com/boostorg/vmd/blob/39ec67f0350f27baf9fda2a413637486c4bc3c7a/include/boost/vmd/detail/is_empty.hpp#L14) or [this line](https://github.com/boostorg/preprocessor/blob/aa8f347df2b189b14c4079c6fb7fa2819ac4a6f1/include/boost/preprocessor/facilities/detail/is_empty.hpp#L19).  
  
Example:  
  
With a file main.cpp:  
```  
#define SUM(a, b)    ((a)+(b))  
  
int main(void)  
{  
    return SUM(1,2,3);  // note the extra arg 3  
}  
```  
  
compiled with  
  
`C:\tmp>"C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.30.30705\bin\Hostx64\x64\cl.exe" /we4002 /I. /EHsc main.cpp`  
  
it produce error:  
```  
Compilateur d'optimisation Microsoft (R) C/C++ version 19.30.30706 pour x64  
Copyright (C) Microsoft Corporation. Tous droits réservés.  
  
main.cpp  
main.cpp(5): error C4002: trop d'arguments pour l'appel de macro de type fonction 'SUM'  
```  
(this is the expected behavior)  
  
but if the file use boost unit_test.hpp:  
  
```  
#define BOOST_TEST_MODULE MyTest  
#include <boost/test/unit_test.hpp>  
  
#define SUM(a, b)    ((a)+(b))  
  
int main(void)  
{  
    return SUM(1,2,3);  // note the extra arg 3  
}  
```  
  
compiled with the same command line  
  
`C:\tmp>"C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.30.30705\bin\Hostx64\x64\cl.exe" /we4002 /I. /EHsc main.cpp`  
  
The extra argument is now accepted by producing only a warning:  
  
```  
Compilateur d'optimisation Microsoft (R) C/C++ version 19.30.30706 pour x64  
Copyright (C) Microsoft Corporation. Tous droits réservés.  
  
main.cpp  
main.cpp(8): warning C4002: trop d'arguments pour l'appel de macro de type fonction 'SUM'  
... <try to link>  
```
