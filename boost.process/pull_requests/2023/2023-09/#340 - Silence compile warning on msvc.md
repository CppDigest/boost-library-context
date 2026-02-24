# #340 Silence compile warning on msvc [Merged]

> Username: Shauren  
> Created at: 2023-09-12 22:17:48 UTC  
> Updated at: 2023-09-14 06:26:47 UTC  
> Merged at: 2023-09-14 00:09:35 UTC  
> Closed at: 2023-09-14 00:09:35 UTC  
> Url: https://github.com/boostorg/process/pull/340  

Warning  
```  
1>StartProcess.cpp  
1>C:\Program Files\boost_1_83_0\boost/process/pipe.hpp(132,5): warning C4297: 'boost::process::basic_pipebuf<char,std::char_traits<char>>::~basic_pipebuf': function assumed not to throw an exception but does  
1>C:\Program Files\boost_1_83_0\boost/process/pipe.hpp(132,5): message : destructor or deallocator has a (possibly implicit) non-throwing exception specification  
1>C:\Program Files\boost_1_83_0\boost/process/pipe.hpp(124,6): message : while compiling class template member function 'boost::process::basic_pipebuf<char,std::char_traits<char>>::~basic_pipebuf(void)'  
1>C:\Program Files\boost_1_83_0\boost/process/pipe.hpp(304,42): message : see reference to class template instantiation 'boost::process::basic_pipebuf<char,std::char_traits<char>>' being compiled  
1>C:\Users\shaur\Desktop\Sources\TrinityCore\src\common\Utilities\StartProcess.cpp(85,14): message : see reference to class template instantiation 'boost::process::basic_ipstream<char,std::char_traits<char>>' being compiled  
```  
  
cl.exe version  
```  
Microsoft (R) C/C++ Optimizing Compiler Version 19.37.32822 for x64  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-09-13 00:14:00 UTC  
> Url: https://github.com/boostorg/process/pull/340#issuecomment-1716747734  

Thanks. This has to be a weird compiler detial.

---

## Comment 2

> Username: Shauren  
> Created_at: 2023-09-13 07:52:26 UTC  
> Url: https://github.com/boostorg/process/pull/340#issuecomment-1717125618  

Weird compiler detail or not, this warning impacts my CI jobs with treat-warnings-as-errors enabled

---
