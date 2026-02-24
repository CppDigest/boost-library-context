# #396 - shared_ptr_from_python.hpp shadow storage compiler warning [Open]

> Username: chcg  
> Created at: 2022-10-01 15:31:00 UTC  
> Updated at: 2022-10-01 15:37:19 UTC  
> Url: https://github.com/boostorg/python/issues/396  

Please check change to shared_ptr_from_python.hpp from https://github.com/boostorg/python/commit/aca3c80c4f80dd3d52219e1ee299e08bb980bcfd  
  
https://github.com/boostorg/python/blob/47d5bc76f69e20625214381c930a2fad5765e2b3/include/boost/python/converter/shared_ptr_from_python.hpp#L44-L65  
  
> C:\projects\pythonscript\packages\boost.1.80.0\lib\native\include\boost\python\converter\shared_ptr_from_python.hpp(52,1): warning C4456: declaration of 'storage' hides previous local declaration (compiling source file ..\src\NotepadPython.cpp) [C:\projects\pythonscript\PythonScript\project\PythonScript2010.vcxproj]  
>        C:\projects\pythonscript\packages\boost.1.80.0\lib\native\include\boost\python\converter\shared_ptr_from_python.hpp(46,17): message : see declaration of 'storage' (compiling source file ..\src\NotepadPython.cpp) [C:\projects\pythonscript\PythonScript\project\PythonScript2010.vcxproj]  
>        C:\projects\pythonscript\packages\boost.1.80.0\lib\native\include\boost\python\converter\shared_ptr_from_python.hpp(45): message : while compiling class template member function 'void boost::python::converter::shared_ptr_from_python<T,std::shared_ptr>::construct(PyObject *,boost::python::converter::rvalue_from_python_stage1_data *)' [C:\projects\pythonscript\PythonScript\project\PythonScript2010.vcxproj]  
>   
  
From https://ci.appveyor.com/project/bruderstein/pythonscript/builds/44940991/job/g43lxe0hqbj7lrhx build with MS VS2022.  
  
Looks like a merge issue.
