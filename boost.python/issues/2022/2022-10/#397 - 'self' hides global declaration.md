# #397 - 'self' hides global declaration [Open]

> Username: chcg  
> Created at: 2022-10-01 15:47:27 UTC  
> Updated at: 2022-10-01 15:47:27 UTC  
> Url: https://github.com/boostorg/python/issues/397  

See https://ci.appveyor.com/project/bruderstein/pythonscript/builds/44940991/job/g43lxe0hqbj7lrhx  
  
> C:\projects\pythonscript\packages\boost.1.80.0\lib\native\include\boost\python\object_call.hpp(18,1): warning C4459: declaration of 'self' hides global declaration (compiling source file ..\src\ScintillaWrapper.cpp) [C:\projects\pythonscript\PythonScript\project\PythonScript2010.vcxproj]  
>        C:\projects\pythonscript\packages\boost.1.80.0\lib\native\include\boost\python\self.hpp(24,35): message : see declaration of 'boost::python::self_ns::self' (compiling source file ..\src\ScintillaWrapper.cpp) [C:\projects\pythonscript\PythonScript\project\PythonScript2010.vcxproj]  
>   
  
MS VS2022 compiler warning.
