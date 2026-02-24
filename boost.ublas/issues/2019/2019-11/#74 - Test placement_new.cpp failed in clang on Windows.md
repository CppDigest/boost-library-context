# #74 - Test placement_new.cpp failed in clang on Windows [Open]

> Username: yuxianch  
> Created at: 2019-11-19 07:20:30 UTC  
> Updated at: 2019-11-19 07:20:30 UTC  
> Url: https://github.com/boostorg/ublas/issues/74  

Hi,  
I found that test [placement_new.cpp](https://github.com/boostorg/ublas/tree/master/test/placement_new.cpp) failed in clang on Windows.  
I got 253 as the return value of the exetuable file after running following compiling command.  
```  
ksh-3.2$ clang placement_new.cpp -o placement_new.exe -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -DBOOST_ALL_NO_LIB=1 -DBOOST_UBLAS_NO_EXCEPTIONS -I"..\..\..\.."  
ksh-3.2$ ./placement_new.exe  
0,0  
ksh-3.2$ echo $?  
253  
```  
We can know that this is because macro `BOOST_UBLAS_USEFUL_ARRAY_PLACEMENT_NEW` is not defined, but the value of `array_new_offset` is 0.   
In addition, this test also failed in clang-cl like below but passed in cl on Windows.   
So, is this an issue in the boost code or an bug in the clang compiler?  
```  
ksh-3.2$ cl placement_new.cpp -o placement_new.exe /std:c++17 -D_CRT_USE_BUILTIN_OFFSETOF -DBOOST_ALL_NO_LIB=1 -DBOOST_UBLAS_NO_EXCEPTIONS -I"..\..\..\.."  
Microsoft (R) C/C++ Optimizing Compiler Version 19.22.27905 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
cl : Command line warning D9035 : option 'o' has been deprecated and will be removed in a future release  
placement_new.cpp  
placement_new.cpp: warning C4117: macro name '_CRT_USE_BUILTIN_OFFSETOF' is reserved, '#define' ignored  
c:/Program files (x86)/Microsoft Visual Studio/2019/Professional/VC/Tools/MSVC/14.22.27905/include\ostream(257): warning C4530: C++ exception handler used, but unwind semantics are not enabled. Specify /EHsc  
c:/Program files (x86)/Microsoft Visual Studio/2019/Professional/VC/Tools/MSVC/14.22.27905/include\ostream(248): note: while compiling class template member function 'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(int)'  
placement_new.cpp(43): note: see reference to function template instantiation 'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(int)' being compiled  
placement_new.cpp(43): note: see reference to class template instantiation 'std::basic_ostream<char,std::char_traits<char>>' being compiled  
Microsoft (R) Incremental Linker Version 14.22.27905.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
/out:placement_new.exe  
/out:placement_new.exe  
placement_new.obj  
ksh-3.2$ ./placement_new.exe  
0,8  
ksh-3.2$ echo $?  
0  
```
