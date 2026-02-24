# #984 - Example on how two expose special functions to Python does not compile [Open]

> Username: Volker-Weissmann  
> Created at: 2024-11-22 16:31:15 UTC  
> Updated at: 2024-11-22 16:31:15 UTC  
> Url: https://github.com/boostorg/boost/issues/984  

The [documentation](https://www.boost.org/doc/libs/1_86_0/libs/python/doc/html/tutorial/tutorial/exposing.html) contains many examples on how to use boost for python-c++ interoperation, but one of them does not compile:  
  
```c++  
#include <boost/python.hpp>  
#include <ostream>  
using namespace boost::python;  
using namespace std;  
  
class Rational {  
public:  
  operator double() const;  
};  
  
Rational pow(Rational, Rational);  
Rational abs(Rational);  
ostream &operator<<(ostream &, Rational);  
  
BOOST_PYTHON_MODULE(hello) {  
  
  class_<Rational>("Rational")  
      .def(float_(self))               // __float__  
      .def(pow(self, other<Rational>)) // __pow__  
      .def(abs(self))                  // __abs__  
      .def(str(self))                  // __str__  
      ;  
}  
```  
```  
$ g++ -I/usr/include/python3.12 -fpermissive main.cpp -c  
main.cpp: In function 'void init_module_hello()':  
main.cpp:19:37: error: expected primary-expression before ')' token  
   19 |       .def(pow(self, other<Rational>)) // __pow__  
      |                                     ^  
```  
Removing the `.def(pow(self, other<Rational>)) // __pow__` changes the error:  
  
``` $ g++ -I/usr/include/python3.12 -fpermissive main.cpp -c  
In file included from /usr/include/boost/python/object_core.hpp:20,  
                 from /usr/include/boost/python/args.hpp:22,  
                 from /usr/include/boost/python.hpp:11,  
                 from main.cpp:1:  
/usr/include/boost/python/def_visitor.hpp: In instantiation of 'static void boost::python::def_visitor_access::visit(const V&, classT&) [with V = boost::python::def_visitor<boost::python::api::object>; classT = boost::python::class_<Rational>]':  
/usr/include/boost/python/def_visitor.hpp:67:34:   required from 'void boost::python::def_visitor<DerivedVisitor>::visit(classT&) const [with classT = boost::python::class_<Rational>; DerivedVisitor = boost::python::api::object]'  
   67 |         def_visitor_access::visit(*this, c);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~  
/usr/include/boost/python/class.hpp:221:22:   required from 'boost::python::class_<T, X1, X2, X3>::self& boost::python::class_<T, X1, X2, X3>::def(const boost::python::def_visitor<Derived>&) [with Derived = boost::python::api::object; W = Rational; X1 = boost::python::detail::not_specified; X2 = boost::python::detail::not_specified; X3 = boost::python::detail::not_specified; self = boost::python::class_<Rational>]'  
  221 |         visitor.visit(*this);  
      |         ~~~~~~~~~~~~~^~~~~~~  
main.cpp:20:11:   required from here  
   17 |   class_<Rational>("Rational")  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   18 |       .def(float_(self)) // __float__  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   19 |       .def(abs(self))    // __abs__  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   20 |       .def(str(self))    // __str__  
      |       ~~~~^~~~~~~~~~~  
/usr/include/boost/python/def_visitor.hpp:31:34: error: no matching function for call to 'boost::python::api::object::visit(boost::python::class_<Rational>&) const'  
   31 |         v.derived_visitor().visit(c);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~^~~  
/usr/include/boost/python/object_core.hpp:160:12: note: candidate: 'template<class ClassT, class DocStringT> void boost::python::api::object_operators<U>::visit(ClassT&, const char*, const boost::python::detail::def_helper<DocStringT>&) const [with DocStringT = ClassT; U = boost::python::api::object]'  
  160 |       void visit(ClassT& cl, char const* name, python::detail::def_helper<DocStringT> const& helper) const  
      |            ^~~~~  
/usr/include/boost/python/object_core.hpp:160:12: note:   candidate expects 3 arguments, 1 provided  
```  
  
  
I use boost 1.86.0 and gcc 14.2.1, both installed using the Arch Linux package manager. Adding `--std=c++11`, `--std=c++14`, `--std=c++17` or `--std=c++20` does not solve the issue.
