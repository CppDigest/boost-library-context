# #345 - Using Boost.NumPy throws unresolved extern symbol link errors. [Closed]

> Username: bernd-k1337  
> Created at: 2021-01-12 11:05:12 UTC  
> Updated at: 2022-09-16 13:55:28 UTC  
> Closed at: 2022-09-16 13:55:28 UTC  
> Url: https://github.com/boostorg/python/issues/345  

Hi Guys, I'm using normal Boost.NumPy functions in C++ like ndarray::get_data(), and I get a "reference to unresolved external symbol" error. The .lib's are linked and I have no approach on how to solve the error.  
  
I am using Boost 1.74.  
  
Here is a Code example:  
  
```cpp  
#include<boost/python/numpy.hpp>  
using namespace boost;  
  
void func(python::object _obj)  
{  
    python::numpy::ndarray my_ndarray = python::extract<boost::python::numpy::ndarray>(_obj.attr("Member_01"));  
  
    char* data = my_ndarray.get_data();  
}  
  
BOOST_PYTHON_MODULE(ogwa_acc)   
{       
    python::def("func", func);  
}   
```  
  
Thats the one of the unresolved symbols: "`"__declspec(dllimport) public: char * __cdecl boost::python::numpy::ndarray::get_data(void)const " (_imp?get_data@ndarray@numpy@python@boost@@QEBAPEADXZ)`"  
  
I am using the normal Visual Studio linker and this is my linker config:  
  
`  
/OUT:"D:\OthelloGame_withAI\x64\Debug\ogwa_acc.pyd" /MANIFEST /NXCOMPAT /PDB:"D:\OthelloGame_withAI\x64\Debug\ogwa_acc.pdb" /DYNAMICBASE "boost_numpy38-vc142-mt-gd-x64-1_74.lib" "boost_numpy38-vc142-mt-x64-1_74.lib" "boost_python38-vc142-mt-gd-x64-1_74.lib" "boost_python38-vc142-mt-x64-1_74.lib" "kernel32.lib" "user32.lib" "gdi32.lib" "winspool.lib" "comdlg32.lib" "advapi32.lib" "shell32.lib" "ole32.lib" "oleaut32.lib" "uuid.lib" "odbc32.lib" "odbccp32.lib" /IMPLIB:"D:\OthelloGame_withAI\x64\Debug\ogwa_acc.lib" /DEBUG /DLL /MACHINE:X64 /INCREMENTAL /PGD:"D:\OthelloGame_withAI\x64\Debug\ogwa_acc.pgd" /SUBSYSTEM:CONSOLE /MANIFESTUAC:"level='asInvoker' uiAccess='false'" /ManifestFile:"x64\Debug\ogwa_acc.pyd.intermediate.manifest" /ERRORREPORT:PROMPT /NOLOGO /LIBPATH:"C:\Program Files\Python38\libs" /LIBPATH:"D:\OthelloGame_withAI\Third-Party\boost_1_74_0\finished_libs" /TLBID:1  
`  
  
`D:\OthelloGame_withAI\Third-Party\boost_1_74_0\finished_libs` is the location of:  
  
`boost_numpy38-vc142-mt-gd-x64-1_74.lib; boost_python38-vc142-mt-gd-x64-1_74.lib; boost_numpy38-vc142-mt-x64-1_74.lib; boost_python38-vc142-mt-x64-1_74.lib`
