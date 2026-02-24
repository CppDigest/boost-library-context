# #192 - Exposing inherited static member variables as static property with the same name leads to exception [Open]

> Username: gabrielbocek  
> Created at: 2018-03-21 13:44:44 UTC  
> Updated at: 2018-03-21 13:44:44 UTC  
> Url: https://github.com/boostorg/python/issues/192  

This bug has been reported by Luke Titley in 2009 on boost-build mailing list. It is still reproducible with the version of boost python 1.65 on Visual C++ 2017. I'm using the Luke's code snippet to show the problem.   
  
There are two boost python classes, one inheriting from the other; each with a static member variable of the same name.  
  
//---------------------------------------------------------------------------------------  
// C++ Classes  
//---------------------------------------------------------------------------------------  
class Base  
{  
public:  
  
    static const wstring Name;  
  
};  
const  wstring Base::Name(L"Base");  
  
//---------------------------------------------------------------------------------------  
class Derived : public Base  
{  
public:  
  
    static const wstring Name;  
  
};  
const  wstring Derived ::Name(L"Derived");  
  
//---------------------------------------------------------------------------------------  
// Boost Python Bindings  
//---------------------------------------------------------------------------------------  
wstring getBaseName()  
{  
    return Base::Name;  
}  
void exposeBase()  
{  
    py::class_<Base>("Base")  
      .add_static_property("Name", &getBaseName) ;  
}  
  
//---------------------------------------------------------------------------------------  
wstring getDerivedName()  
{  
    return Derived::Name;  
}  
void exposeDerived()  
{  
    py::class_<Derived, bases<Base> >("Derived")  
      .add_static_property("Name", &getDerivedName) ;  
}  
  
//---------------------------------------------------------------------------------------  
BOOST_PYTHON_MODULE(nmx)  
{  
  
    exposeBase();  
    exposeDerived();  
}  
  
The import of the module fails and it's caused by throwing exception in exposedDerived (add_static_property).  
  
Best Regards,  
Gabriel
