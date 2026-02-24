# #466 - Exporting wrapped functions doesn't work [Open]

> Username: smurfix  
> Created at: 2025-01-10 05:55:50 UTC  
> Updated at: 2025-01-10 05:55:58 UTC  
> Url: https://github.com/boostorg/python/issues/466  

I find myself in need of exporting a whole lot of functions to Python which require a wrapper (the API needs locking and whatnot).  
  
The following code probably should work (pybind11 can handle it, for the most part), but doesn't.  
  
```  
#include <iostream>  
#include <boost/python.hpp>  
  
template <class R, class... Args>  
struct wrap  
{  
    using funct_type = R(*)(Args...);  
    funct_type func;  
    wrap(funct_type f): func(f) {};  
    R operator()(Args&&... args)  
    {  
          std::cout << "before calling\n";  
          R ret=func(std::forward<Args>(args)...);  
          std::cout << "After calling\n";  
          return ret;  
    }  
};  
  
class bar {};  
class foo : bar {  
    public:  
    enum xfoo { FOO=1, BAR=2, BAZ=3    };  
    static int add(int a, int b, enum xfoo c) { return a+b; }  
    static int add2(int a, int b) { return a+b; }  
};  
  
BOOST_PYTHON_MODULE(example)  
{  
    using namespace boost::python;  
  
    // this works  
    std::cout << "Result" << wrap{foo::add}(1,2, foo::xfoo::FOO) << std::endl;  
  
    // so does this  
    def("add", foo::add, "A function that adds two numbers and no enum");  
  
    // this works with pybind11 but not boost  
    def("add2", wrap{foo::add2}, "A function that adds two numbers");  
  
    // this works with neither  
    def("add", wrap{foo::add}, "A function that adds two numbers and no enum");  
}  
```
