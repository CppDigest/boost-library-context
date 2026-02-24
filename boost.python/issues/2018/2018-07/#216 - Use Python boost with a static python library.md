# #216 - Use Python boost with a static python library [Closed]

> Username: illera88  
> Created at: 2018-07-03 01:11:55 UTC  
> Updated at: 2018-07-03 02:18:08 UTC  
> Closed at: 2018-07-03 02:18:08 UTC  
> Url: https://github.com/boostorg/python/issues/216  

Hi,  
  
I have python 3.7 statically linked as big fat library.   
  
I want to use it along with python boost. I have created the boost `.lib` with the following command:  
  
`C:\boost_1_67_0>.\b2.exe python=3.7 --with-python runtime-link=static toolset=msvc-14.1 runtime-link=static variant=release address-model=64 --build-type=complete -a --user-config="C:\boost_1_67_0\user-config.jam"`  
  
My `cpp` code is a test code:  
  
```c++  
#define BOOST_PYTHON_STATIC_LIB  
#define Py_NO_ENABLE_SHARED  
#include <iostream>  
#include <boost/python.hpp>  
namespace py = boost::python;  
  
int main() {  
	// Must be called before any boost::python functions  
	Py_Initialize();  
	// import the main module  
	py::object main_module = py::import("__main__");  
	// load the dictionary object out of the main module  
	py::object main_namespace = main_module.attr("__dict__");  
	// run simple code within the main namespace using the boost::python::exec   
	//  function  
	py::exec("print 'Hello, world'", main_namespace);  
}  
```  
  
I added the boost and python libs but I'm still getting this errors at link time:  
  
```  
1>Source.obj : error LNK2001: unresolved external symbol _Py_NoneStruct  
1>Source.obj : error LNK2001: unresolved external symbol Py_Initialize  
1>Source.obj : error LNK2001: unresolved external symbol PyEval_CallFunction  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(import.obj) : error LNK2001: unresolved external symbol __imp_PyImport_ImportModule  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(module.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(function.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(tuple.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(function_doc_signature.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(builtin_converters.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(class.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(list.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(pickle_support.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(str.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(object_protocol.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(object_operators.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
1>libboost_python37-vc141-mt-s-x64-1_67.lib(dict.obj) : error LNK2001: unresolved external symbol __imp__Py_NoneStruct  
[...]  
```  
  
Do you have any idea why this may be happening?   
  
Thank you

---

## Comment 1

> Username: illera88  
> Created at: 2018-07-03 02:06:14 UTC  
> Url: https://github.com/boostorg/python/issues/216#issuecomment-401990721  

More info:  
  
```  
C:\\boost_1_67_0>dumpbin /all C:\python37.lib | grep Py_Initialize  
   46059C ??_C@_0BD@PJNPJLDN@_Py_InitializeCore?$AA@  
   46059C ??_C@_0BO@BGMOEEGD@_Py_InitializeMainInterpreter?$AA@  
   46059C ??_C@_0CD@JBOPBLLO@Py_Initialize?5must?5be?5called?5fir@  
   46059C _Py_Initialize  
   46059C _Py_InitializeEx  
   46059C __Py_InitializeCore  
   46059C __Py_InitializeEx_Private  
   46059C __Py_InitializeMainInterpreter  
       1D ??_C@_0BD@PJNPJLDN@_Py_InitializeCore?$AA@  
       1D ??_C@_0BO@BGMOEEGD@_Py_InitializeMainInterpreter?$AA@  
       1D ??_C@_0CD@JBOPBLLO@Py_Initialize?5must?5be?5called?5fir@  
       1D _Py_Initialize  
       1D _Py_InitializeEx  
       1D __Py_InitializeCore  
       1D __Py_InitializeEx_Private  
       1D __Py_InitializeMainInterpreter  
```  
  
It seems that the Python library adds an extra underscode `_` to the symbols making it `_Py_Initialize` instead of `Py_Initialize` as the linker is looking for.   
  
Why is that?

---

## Comment 2

> Username: illera88  
> Created at: 2018-07-03 02:10:42 UTC  
> Url: https://github.com/boostorg/python/issues/216#issuecomment-401991306  

Daaamn! I built python.lib as x86 and boost as x64. My bad!!  
  
Gonna check if it works changing it

---

## Comment 3

> Username: illera88  
> Created at: 2018-07-03 02:18:08 UTC  
> Url: https://github.com/boostorg/python/issues/216#issuecomment-401992380  

Yes it was!
