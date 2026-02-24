# #337 - assertion failed while debugging Boost.Python module [Closed]

> Username: bernd-k1337  
> Created at: 2020-12-19 13:37:43 UTC  
> Updated at: 2022-09-16 13:55:51 UTC  
> Closed at: 2022-09-16 13:55:51 UTC  
> Url: https://github.com/boostorg/python/issues/337  

I want to debug/create this Boost.Python module:  
  
```  
#include <boost/python.hpp>  
  
using namespace boost::python;  
  
void func() { }  
  
BOOST_PYTHON_MODULE(ogwa_acc)   
{   
def("func", func);  
}  
```  
  
  
But every time I get this Message and the module doesn't get created:  
  
```  
Assertion failed!  
  
Program: C:\Program Files\Python38\python38_d.dll   
File: D:\a\1\s\Objects\unicodeobject.c   
Line: 11676   
  
Expression: _Py_HashSecret_Initialized   
  
For information on how your program can cause an assertion failure, see the Visual C++ documentation on asserts   
  
(Press Retry to debug the application - JIT must be enabled)  
```  
  
  
Unfortunately, I can not do anything with this message. For example, I do not have a folder "D:\a".  
  
My stack trace is as following:  
  
```  
ucrtbased.dll!00007ff88b641235() unknown  
  
ucrtbased.dll!00007ff88b641053() unknown  
  
ucrtbased.dll!00007ff88b64399f() unknown  
  
python38_d.dll!unicode_hash(_object * self) line 11676  
  
[extern code]  
  
ogwa_acc.pyd!boost::python::objects::function::add_to_namespace(const boost::python::api::object & name_space, const char * name_, const boost::python::api::object & attribute, const char * doc) line 448  
  
ogwa_acc.pyd!boost::python::objects::add_to_namespace(const boost::python::api::object & name_space, const char * name, const boost::python::api::object & attribute, const char * doc) line 565  
  
ogwa_acc.pyd!boost::python::detail::scope_setattr_doc(const char * name, const boost::python::api::object & x, const char * doc) line 36  
  
ogwa_acc.pyd!boost::python::def<void (__cdecl*)(void)>(const char * name, void(*)() fn) line 91  
  
ogwa_acc.pyd!init_module_ogwa_acc() line 25  
  
ogwa_acc.pyd!boost::detail::function::void_function_ref_invoker0<void (__cdecl*)(void),void>::invoke(boost::detail::function::function_buffer & function_obj_ptr) line 194  
  
ogwa_acc.pyd!boost::function0<void>::operator()() line 765  
  
ogwa_acc.pyd!boost::python::handle_exception_impl(boost::function0<void> f) line 26  
  
ogwa_acc.pyd!boost::python::handle_exception<void (__cdecl*)(void)>(void(*)() f) line 30  
  
ogwa_acc.pyd!boost::python::detail::\anonymous namespace'::init_module_in_scope(_object * m, void(*)() init_function) line 24  
  
ogwa_acc.pyd!boost::python::detail::init_module(PyModuleDef & moduledef, void(*)() init_function) line 46  
  
ogwa_acc.pyd!PyInit_ogwa_acc() line 20  // BOOST_PYTHON_MODULE(ogwa_acc)  
```  
  
I am grateful for any help! Thank you so much!
