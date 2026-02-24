# #415 - Base class of non exposed derived class in Python side? [Open]

> Username: LudoBar  
> Created at: 2023-04-21 20:58:42 UTC  
> Updated at: 2023-04-21 20:58:42 UTC  
> Url: https://github.com/boostorg/python/issues/415  

Hello, I have a use case where a polymorphism factory can instantiate object of a non exposed class. I have a multi-level inheritance scheme and the last level is not exposed to Python. What I try to understand is why the Python object of the last level takes the type of the top-most class in the inheritance hierarchy and not the one just above?  
  
Here is a minimal example to reproduce the behavior:  
  
C++ side  
```  
class Base  
{  
public:  
    Base() {}  
    virtual ~Base() = default;  
};  
  
class Derived: public Base  
{  
public:  
    Derived(int value) { m_value = value; }  
    ~Derived() = default;  
  
    int getValue() { return m_value; }  
  
private:  
    int m_value;  
};  
  
class DerivedTwo: public Derived  
{  
public:  
    DerivedTwo(int value): Derived(value) {}  
    ~DerivedTwo() = default;  
};  
  
std::shared_ptr<Base> factory()  
{  
    return std::make_shared<Derived>(9);  
}  
  
std::shared_ptr<Base> factoryTwo()  
{  
    return std::make_shared<DerivedTwo>(99);  
}  
  
BOOST_PYTHON_MODULE(mymodule)  
{  
    using namespace boost::python;  
  
    class_<Base, std::shared_ptr<Base>, boost::noncopyable>("Base", init<>());  
  
    class_<Derived, bases<Base>, std::shared_ptr<Derived>, boost::noncopyable>("Derived", init<int>())  
        .def("get_value", &Derived::getValue)  
    ;  
  
    def("factory", &factory);  
    def("factory_two", &factoryTwo);  
}  
```  
Python side  
```  
import mymodule  
  
obj = mymodule.factory()  
print(type(obj))  
  
obj2 = mymodule.factory_two()  
print(type(obj2))  
```  
Output  
  
```  
<class 'mymodule.Derived'>  
<class 'mymodule.Base'> <-- WHY?   
```  
Obviously, if I expose DerivedTwo class, all is OK.   
Thanks for your help.
