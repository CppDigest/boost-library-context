# #173 - Compiling error boost/core/type_name.hpp with /Zc:wchar_t- on Windows using MSVC to compile [Closed]

> Username: SerhiiPavlyk  
> Created at: 2024-05-28 16:12:02 UTC  
> Updated at: 2024-05-28 19:29:38 UTC  
> Closed at: 2024-05-28 19:23:58 UTC  
> Url: https://github.com/boostorg/core/issues/173  

Hi everyone.  
I have an issue after updating my code from the 1.69.0 version to the 1.85.0 version. I need to compile some libs with /Zc:wchar_t-, but there is a compile error after the boost update.  
Here is a PoC to reproduce the error:  
```c++  
#include <boost/core/type_name.hpp>  
  
int main()  
{  
  
}  
```  
And the results of the build using the Microsoft C/C++ compiler.  
  
```  
 cl /EHsc /I"D:\libs\boost_1_85_0" "main.cpp" /out:output.exe  
  
Microsoft (R) C/C++ Optimizing Compiler Version 19.16.27051 for x86  
Copyright (C) Microsoft Corporation.  All rights reserved.  
   
   
main.cpp  
Microsoft (R) Incremental Linker Version 14.16.27051.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
   
/out:main.exe  
/out:ut:output.exe  
main.obj  
   
C:\Windows\System32>cl /EHsc /Zc:wchar_t- /I"D:\libs\boost_1_85_0" "main.cpp" /out:output.exe  
Microsoft (R) C/C++ Optimizing Compiler Version 19.16.27051 for x86  
Copyright (C) Microsoft Corporation.  All rights reserved.  
   
main.cpp  
D:\libs\boost_1_85_0\boost/core/type_name.hpp(354): error C2766: explicit specialization; 'boost::core::detail::tn_holder<unsigned short>' has already been defined  
D:\libs\boost_1_85_0\boost/core/type_name.hpp(273): note: see previous definition of 'tn_holder<unsigned short>'  
```   
As you can see, if you build without the /Zc:wchar_t- flag, the build is successful, but if you build with it, you get a compilation error. Maybe someone has encountered this?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-28 19:03:42 UTC  
> Url: https://github.com/boostorg/core/issues/173#issuecomment-2135924818  

Should be fixed by https://github.com/boostorg/core/commit/3f36d507f2cd9ba55716336c3fb1843d951830ae.

---

## Comment 2

> Username: SerhiiPavlyk  
> Created at: 2024-05-28 19:23:59 UTC  
> Url: https://github.com/boostorg/core/issues/173#issuecomment-2135955497  

Thank you very much! I hope these changes will be added in the next version of the boost.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-05-28 19:29:37 UTC  
> Url: https://github.com/boostorg/core/issues/173#issuecomment-2135964254  

They will be.
