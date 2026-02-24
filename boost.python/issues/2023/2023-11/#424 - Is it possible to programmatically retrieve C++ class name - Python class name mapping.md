# #424 - Is it possible to programmatically retrieve C++ class name / Python class name mapping ? [Open]

> Username: Gregwar  
> Created at: 2023-11-01 08:12:16 UTC  
> Updated at: 2023-11-01 08:19:51 UTC  
> Url: https://github.com/boostorg/python/issues/424  

Hello,  
  
Suppose I do the following:  
  
```c++  
class_<somevendor::SomeClass>("SomePyClass");  
```  
  
Is there a way at runtime in Python to retrieve the mapping between `SomePyClass` and `somevendor::SomeClass` ?  
  
For context I am writing some code to parse Doxygen XML files and producing the `.pyi` file to get more auto-completion and docstrings

---

## Comment 1

> Username: Gregwar  
> Created at: 2023-11-01 08:13:16 UTC  
> Updated at: 2023-11-01 08:19:51 UTC  
> Url: https://github.com/boostorg/python/issues/424#issuecomment-1788572447  

Currently I am wrapping my calls to `class_` with some methods like:  
  
```c++  
template <class T, class DT>  
class_<T> class__(const char* name, init_base<DT> const& init)  
{  
  class_<T> c = class_<T>(name, init);  
  register_type(typeid(T).name(), boost::python::extract<std::string>(c.attr("__name__")));  
  return c;  
}  
```  
  
(Where ` register_type`  stores the two strings in a map which is itself exposed)  
This works but is not really satisfying
