# #439 - How to Properly Expose Enum Comparison Operators to Python Using Boost.Python? [Closed]

> Username: MasterBe  
> Created at: 2024-05-26 15:44:43 UTC  
> Updated at: 2024-05-27 10:40:34 UTC  
> Closed at: 2024-05-27 10:40:34 UTC  
> Url: https://github.com/boostorg/python/issues/439  

I'm trying to expose a C++ enum and its comparison operators to Python using Boost.Python. I've defined the enum and comparison operators as shown in the code below:  
  
```  
///////color.hpp  
enum Color {  
    Red,  
    Green,  
    Blue  
};  
  
bool operator<(const Color c1, const Color c2)  
{  
    return static_cast<int>(c1) < static_cast<int>(c2);  
}  
  
///////color_python.hpp  
#include <boost/python.hpp>  
#include <color.hpp>  
  
bool ltColor(const Color& c1, const Color& c2)  
{  
    return c1 < c2;  
}  
  
BOOST_PYTHON_MODULE(my_module)  
{  
    using namespace boost::python;  
  
    enum_<Color>("Color")  
        .value("Red", Red)  
        .value("Green", Green)  
        .value("Blue", Blue);  
  
    // Expose comparison operators  
    def("__lt__", &ltColor)  
    //other operators i.e. __le__ ...  
    ;  
}  
```  
  
This compiles fine but **looks like python defines these comparison automatically** hence does not care about my definitions at all and even if I throw in ```ltColor``` it does not throw in jupyter for example.  
  
**Still** when I include the color.hpp in another file user.cpp, the code does not even compile:  
  
```  
///////user.cpp  
#include <color.hpp>  
  
void my_function()  
{  
   //...  
}  
```  
  
I get:  
```error LNK2005: "bool __cdecl operator<(enum Color,enum Color)" (??....) already defined in color_python.cpp.obj```

---

## Comment 1

> Username: MasterBe  
> Created at: 2024-05-27 10:40:34 UTC  
> Url: https://github.com/boostorg/python/issues/439#issuecomment-2133196104  

Solution: The operator has to be static
