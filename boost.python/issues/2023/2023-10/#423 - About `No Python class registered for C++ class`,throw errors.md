# #423 - About `No Python class registered for C++ class`,throw errors. [Closed]

> Username: shelllet  
> Created at: 2023-10-27 06:53:18 UTC  
> Updated at: 2023-10-27 08:41:55 UTC  
> Closed at: 2023-10-27 08:41:55 UTC  
> Url: https://github.com/boostorg/python/issues/423  

````  
class Strs {  
std::vector<Str> m_cc;  
  
public:  
   ...  
   Str [](int offset) { return m_cc[offset];}  
   ...  
};  
  
class Str{};  
  
struct StrToPython  
{  
  static PyObject* convert(const Str& b)  
  {  
      ...  
  }  
};  
  
  
BOOST_PYTHON_MODULE(pytype_function_ext)  
{  
  class_<Strs >("Strs") ;  
  
  to_python_converter< Str , StrToPython>();  
}  
````  
## test code  
  
py::object list(Strs());  
  
py::object s1 = list[0];// (1), throw "No Python class registered for C++ class Str"  
  
py::object s2(Str()) // ok！it's call `StrToPython::convert`.  
  
becuase `to_python_converter` does't set `m_class_object` value. so  (1) throw errors.  
  
  
how to resolve this problem?

---

## Comment 1

> Username: shelllet  
> Created at: 2023-10-27 08:41:55 UTC  
> Url: https://github.com/boostorg/python/issues/423#issuecomment-1782530493  

By default indexed elements are returned by proxy. disabled by supplying *true* in the NoProxy template parameter.
