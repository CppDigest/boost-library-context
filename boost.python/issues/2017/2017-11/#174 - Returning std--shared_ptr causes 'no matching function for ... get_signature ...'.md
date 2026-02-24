# #174 - Returning std::shared_ptr causes 'no matching function for ... get_signature ...' [Closed]

> Username: mg262  
> Created at: 2017-11-06 14:04:25 UTC  
> Updated at: 2017-11-06 14:35:43 UTC  
> Closed at: 2017-11-06 14:33:41 UTC  
> Url: https://github.com/boostorg/python/issues/174  

Minimal example causing the bug under 1.63 (+ gcc 6.4):   
  
```  
#include <memory>  
#include <boost/python.hpp>  
  
using namespace boost::python;  
  
std::shared_ptr<int> return_ptr() { throw; }  
  
BOOST_PYTHON_MODULE (w3w_python) {  
    def("return_ptr", return_ptr());  
}  
```  
  
Bug:   
  
> In file included from /usr/include/boost/python/data_members.hpp:15:0,  
>                  from /usr/include/boost/python/class.hpp:17,  
>                  from /usr/include/boost/python.hpp:18,  
>                  from /home/Mohan/python-bindings-core-sdk/main.cpp:3:  
> /usr/include/boost/python/make_function.hpp: In instantiation of ‘boost::python::api::object boost::python::make_function(F) [with F = std::shared_ptr<int>]’:  
> /usr/include/boost/python/def.hpp:82:58:   required from ‘boost::python::api::object boost::python::detail::make_function1(T, ...) [with T = std::shared_ptr<int>]’  
> /usr/include/boost/python/def.hpp:91:59:   required from ‘void boost::python::def(const char*, Fn) [with Fn = std::shared_ptr<int>]’  
> /home/Mohan/python-bindings-core-sdk/main.cpp:11:35:   required from here  
> /usr/include/boost/python/make_function.hpp:104:57: error: no matching function for call to ‘get_signature(std::shared_ptr<int>&)’  
>          f,default_call_policies(), detail::get_signature(f));

---

## Comment 1

> Username: mg262  
> Created at: 2017-11-06 14:21:39 UTC  
> Url: https://github.com/boostorg/python/issues/174#issuecomment-342162461  

I've also tried `register_ptr_to_python`, but that doesn't help.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-11-06 14:30:44 UTC  
> Url: https://github.com/boostorg/python/issues/174#issuecomment-342165226  

The line   
```  
def("return_ptr", return_ptr());  
```  
is wrong.  Do you mean   
```  
def("return_ptr", return_ptr);  
```  
?

---

## Comment 3

> Username: mg262  
> Created at: 2017-11-06 14:33:41 UTC  
> Updated at: 2017-11-06 14:35:43 UTC  
> Url: https://github.com/boostorg/python/issues/174#issuecomment-342166089  

D'oh!  Copy/paste error...  feel really stupid now. Thank you.
