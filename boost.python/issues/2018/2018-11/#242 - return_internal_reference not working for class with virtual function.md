# #242 - return_internal_reference not working for class with virtual function [Open]

> Username: alex8361  
> Created at: 2018-11-06 20:57:28 UTC  
> Updated at: 2018-11-06 20:57:28 UTC  
> Url: https://github.com/boostorg/python/issues/242  

I wrap the template classes A and B through boost python, and try to cast between them. I implement the toB function and wrap it with return_internal_reference<> to achieve this. However, it doesn't return the B object in python, but returns the "dead" A object, of which member function can't be used anymore.  
  
    #include <boost/python.hpp>  
    #include <iostream>  
    using namespace boost::python;  
      
    struct Bar  
    {  
      virtual void hi() { std::cout << "Hi Bar." << std::endl; }  
      virtual ~Bar() {}  
    };  
      
    template<typename T>  
    struct Foo : Bar  
    {  
      void set(T v) { val = v; }  
      T get() { return val; }  
    private:  
      T val;  
    };  
      
    using A = Foo<unsigned short>;  
    using B = Foo<short>;  
      
    B & toB(A & a) { return *reinterpret_cast<B*>(&a);}  
      
    BOOST_PYTHON_MODULE(example)  
    {  
      class_<A>("A")  
        .def("set", &A::set)  
        .def("get", &A::get)  
      ;  
      class_<B>("B")  
        .def("set", &B::set)  
        .def("get", &B::get)  
      ;  
      def("toB", &toB, return_internal_reference<>());  
    }  
Python execution  
  
    >>> from example import *  
    >>> a = A()  
    >>> toB(a)  
    <example.A object at 0x7f7139cd27c0>  
    >>> toB(a).get()  
    Traceback (most recent call last):  
      File "<stdin>", line 1 in <module>  
    Boost.Python.ArgumentError: Python argument types in   
        A.get(A)  
    did not match C++ signature:  
        get(Foo<unsigned int> {lvalue})  
  
It works well if I remove the "virtual" keywords in Bar.
