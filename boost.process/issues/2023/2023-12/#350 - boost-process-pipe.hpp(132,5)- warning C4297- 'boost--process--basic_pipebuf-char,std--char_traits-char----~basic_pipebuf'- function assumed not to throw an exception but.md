# #350 - boost\process\pipe.hpp(132,5): warning C4297: 'boost::process::basic_pipebuf<char,std::char_traits<char>>::~basic_pipebuf': function assumed not to throw an exception but does [Open]

> Username: BullyWiiPlaza  
> Created at: 2023-12-31 17:51:56 UTC  
> Updated at: 2023-12-31 17:51:56 UTC  
> Url: https://github.com/boostorg/process/issues/350  

Hi, I'm getting the following compilation warning in Visual Studio (I'm compiling with `/W4`):  
```  
1>Main.cpp  
1>C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(132,5): warning C4297: 'boost::process::basic_pipebuf<char,std::char_traits<char>>::~basic_pipebuf': function assumed not to throw an exception but does  
1>(compiling source file 'Main.cpp')  
1>C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(132,5):  
1>destructor or deallocator has a (possibly implicit) non-throwing exception specification  
1>C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(132,5):  
1>the template instantiation context (the oldest one first) is  
1>	...  
1>	see reference to class template instantiation 'boost::process::basic_ipstream<char,std::char_traits<char>>' being compiled  
1>	C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(304,42):  
1>	see reference to class template instantiation 'boost::process::basic_pipebuf<char,std::char_traits<char>>' being compiled  
1>	C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(124,6):  
1>	while compiling class template member function 'boost::process::basic_pipebuf<char,std::char_traits<char>>::~basic_pipebuf(void)'  
1>		C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(402,1):  
1>		see the first reference to 'boost::process::basic_pipebuf<char,std::char_traits<char>>::~basic_pipebuf' in 'boost::process::basic_ipstream<char,std::char_traits<char>>::~basic_ipstream'  
```
