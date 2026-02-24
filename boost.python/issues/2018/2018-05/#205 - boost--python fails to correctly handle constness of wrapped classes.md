# #205 - boost::python fails to correctly handle constness of wrapped classes [Open]

> Username: rhaschke  
> Created at: 2018-05-27 15:47:19 UTC  
> Updated at: 2023-09-18 10:26:43 UTC  
> Url: https://github.com/boostorg/python/issues/205  

I want to wrap C++ code that only provides `const` access to a class `Foo`:  
  
```cpp  
struct Foo {};  
std::shared_ptr<const Foo> producer();  
```  
Wrapping `Foo` as usual:  
```cpp  
BOOST_PYTHON_MODULE(foo) {  
    boost::python::class_<Foo, std::shared_ptr<Foo>, boost::noncopyable>("Foo", boost::python::no_init);  
    boost::python::def("producer", &producer);  
}  
```  
compiles fine, but complains at runtime - obviously - about a missing type converter:  
`No to_python (by-value) converter found for C++ type: std::shared_ptr<Foo const>`  
  
Alternatively trying:  
```cpp  
BOOST_PYTHON_MODULE(foo) {  
    // const holder  
    boost::python::class_<Foo, std::shared_ptr<const Foo>, boost::noncopyable>("Foo", boost::python::no_init);  
    // const class + holder  
    boost::python::class_<const Foo, std::shared_ptr<const Foo>, boost::noncopyable>("Foo", boost::python::no_init);  
}  
```  
yields compile-time errors about constness issues. Isn't there a solution available that respects constness?

---

## Comment 1

> Username: allopislozano  
> Created at: 2023-09-18 10:24:48 UTC  
> Url: https://github.com/boostorg/python/issues/205#issuecomment-1723133551  

Same problem here , was any solution found?

---

## Comment 2

> Username: rhaschke  
> Created at: 2023-09-18 10:26:43 UTC  
> Url: https://github.com/boostorg/python/issues/205#issuecomment-1723136915  

I switched to [pybind11](https://github.com/pybind/pybind11), which is a more modern and faster wrapper.
