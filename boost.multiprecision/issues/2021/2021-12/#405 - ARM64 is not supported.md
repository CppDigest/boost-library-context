# #405 - ARM64 is not supported? [Closed]

> Username: Kojoley  
> Created at: 2021-12-16 22:26:24 UTC  
> Updated at: 2021-12-19 09:35:16 UTC  
> Closed at: 2021-12-19 09:35:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/405  

```  
Microsoft Windows [Version 10.0.19043.1348]  
(c) Microsoft Corporation. All rights reserved.  
  
G:\boost_1_78_0>"C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsx86_arm64.bat"  
**********************************************************************  
** Visual Studio 2019 Developer Command Prompt v16.11.8  
** Copyright (c) 2021 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x86_arm64'  
  
G:\boost_1_78_0>cl /c cpp_int_test.cpp /I.  
Microsoft (R) C/C++ Optimizing Compiler Version 19.29.30138 for ARM64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
main.cpp  
.\boost/multiprecision/cpp_int/intel_intrinsics.hpp(118): error C3861: '_addcarry_u32': identifier not found  
.\boost/multiprecision/cpp_int/intel_intrinsics.hpp(123): error C3861: '_subborrow_u32': identifier not found  
```  
  
`cpp_int_test.cpp`:  
```  
#include <boost/multiprecision/cpp_int.hpp>  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-12-18 17:00:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/405#issuecomment-997230764  

I had the preprocessor logic incorrect.  The referenced patch fixes this locally for me.
