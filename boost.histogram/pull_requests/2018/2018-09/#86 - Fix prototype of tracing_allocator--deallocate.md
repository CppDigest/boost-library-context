# #86 Fix prototype of tracing_allocator::deallocate [Merged]

> Username: mloskot  
> Created at: 2018-09-17 17:32:53 UTC  
> Updated at: 2018-09-18 07:49:29 UTC  
> Merged at: 2018-09-18 07:49:24 UTC  
> Closed at: 2018-09-18 07:49:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/86  

Align the parameter types with `std::allocator::deallocate prototype.  
Otherwise, compilation using MSVC 19.15.26729 fails due to unresolved conversion of the first argument from `T* const` to `T*&`  
  
-----  
  
Compilation of `axis_test.cpp` with MSVC 19.15.26729 (VS2017 15.8.4) is failing with  
  
```  
...  
ClCompile:  
  C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.15.26726\bin\HostX64\x86\CL.exe   
  /c /ID:\boost.win\libs\histogram\build\..\include /ID:\boost.win /Zi /nologo /W3 /WX- /diagnostics:classic /Od /Ob0 /Oy-  
  /D WIN32 /D _WINDOWS /D _SCL_SECURE_NO_WARNINGS /D "CMAKE_INTDIR=\"Debug\"" /D _MBCS /Gm- /EHsc /RTC1 /MDd /GS /fp:precise   
  /Zc:wchar_t /Zc:forScope /Zc:inline /GR /Fo"axis_test.dir\Debug\\" /Fd"axis_test.dir\Debug\vc141.pdb" /Gd /TP /analyze-   
  /FC /errorReport:queue D:\boost.win\libs\histogram\test\axis_test.cpp  
  axis_test.cpp  
c:\program files (x86)\microsoft visual studio\2017\professional\vc\tools\msvc\14.15.26726\include\vector(1505):  
  error C2664: 'void boost::histogram::tracing_allocator<axis_type>::deallocate(T *&,size_t)':  
    cannot convert argument 1 from 'boost::histogram::axis::any<T1,T2,T3,T4,T5> *const ' to 'T *&'  
    [D:\boost.win\libs\histogram\build\_build.vs2017\axis_test.vcxproj]  
          with  
          [  
              T=axis_type  
          ]  
  c:\program files (x86)\microsoft visual studio\2017\professional\vc\tools\msvc\14.15.26726\include\vector(1505):  
    note: Conversion loses qualifiers  
  c:\program files (x86)\microsoft visual studio\2017\professional\vc\tools\msvc\14.15.26726\include\vector(1497):  
    note: while compiling class template member function  
      'void std::vector<axis_type,boost::histogram::tracing_allocator<axis_type>>::_Reallocate_exactly(const unsigned int)'  
  c:\program files (x86)\microsoft visual studio\2017\professional\vc\tools\msvc\14.15.26726\include\vector(1522):  
    note: see reference to function template instantiation  
      'void std::vector<axis_type,boost::histogram::tracing_allocator<axis_type>>::_Reallocate_exactly(const unsigned int)'  
        being compiled  
  d:\boost.win\libs\histogram\test\axis_test.cpp(462):  
    note: see reference to class template instantiation  
      'std::vector<axis_type,boost::histogram::tracing_allocator<axis_type>>' being compiled  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-09-17 17:37:56 UTC  
> Url: https://github.com/boostorg/histogram/pull/86#issuecomment-422105225  

[![Coverage Status](https://coveralls.io/builds/19040304/badge)](https://coveralls.io/builds/19040304)  
  
Coverage remained the same at 99.499% when pulling **f1b7ebfb0a68ae8073a3c728a5430e6290fbd5d4 on mloskot:ml/fix-tracing_allocator-for-vs2017** into **a1569b59af870fd0c60f71011edb0b5c7e63f84b on HDembinski:develop**.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2018-09-18 07:49:29 UTC  
> Url: https://github.com/boostorg/histogram/pull/86#issuecomment-422291320  

Thanks!

---
