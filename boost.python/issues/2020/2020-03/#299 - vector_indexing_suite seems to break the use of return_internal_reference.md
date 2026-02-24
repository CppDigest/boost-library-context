# #299 - vector_indexing_suite seems to break the use of return_internal_reference [Open]

> Username: NormanT7  
> Created at: 2020-03-31 13:12:36 UTC  
> Updated at: 2020-04-08 11:54:36 UTC  
> Url: https://github.com/boostorg/python/issues/299  

I can create a class Foo that returns an internal reference to a class Bar and everything seems to work just fine. However, when I try and expose a vector of Foo using the vector_indexing_suite, I get some weird behavior. In Python, a reference to the underlying Bar of a Foo in a vector of Foos gets corrupted when a new Foo is appended to the vector.  
  
Since most of the code comes straight out of the Boost Python docs, I assume it should work.  
  
The issue can be replicated by making a few modifications to an [example](https://www.boost.org/doc/libs/1_62_0/libs/python/doc/html/reference/function_invocation_and_creation/models_of_callpolicies.html#function_invocation_and_creation.models_of_callpolicies.boost_python_return_internal_ref.example) from the Boost Python docs.  
  
```c++  
#include <boost/python/module.hpp>  
#include <boost/python/class.hpp>  
#include <boost/python/return_internal_reference.hpp>  
#include <boost/python/suite/indexing/vector_indexing_suite.hpp>  
  
  
#include <vector>  
  
class Bar  
{  
 public:  
   Bar(int x) : x(x) {}  
   int get_x() const { return x; }  
   void set_x(int x) { this->x = x; }  
  
   bool operator==(const Bar &other) const { return other.x == x;}  
   bool operator!=(const Bar &other) const { return !(other == (*this)); }  
  
 private:  
   int x;  
};  
  
class Foo  
{  
 public:  
   Foo(int x) : b(x) {}  
  
   // Returns an internal reference  
   Bar const& get_bar() const { return b; }  
  
   bool operator==(const Foo &other) const {return other.b == b;}  
   bool operator!=(const Foo &other) const { return !(other == (*this)); }  
  
 private:  
   Bar b;  
};  
  
using namespace boost::python;  
BOOST_PYTHON_MODULE(boosttest)  
{  
   class_<Bar>("Bar", init<int>())  
      .def("get_x", &Bar::get_x)  
      .def("set_x", &Bar::set_x)  
      ;  
  
   class_<Foo>("Foo", init<int>())  
      .def("get_bar", &Foo::get_bar  
          , return_internal_reference<>())  
      ;  
  
   class_<std::vector<Foo>>("FooList")  
      .def(vector_indexing_suite<std::vector<Foo>>())  
      ;  
}  
```  
  
Then on the Python side, we get the following.   
```  
>>> import boosttest  
>>> foolist = boosttest.FooList()  
>>> foolist.append(boosttest.Foo(2))  
>>> foo_ref = foolist[0]  
>>> bar_ref = foo_ref.get_bar()  
>>> bar_ref.get_x()  
2  
>>> foolist.append(boosttest.Foo(3))  
>>> bar_ref.get_x()  
-572662307  
>>> foo_ref.get_bar().get_x()  
2  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2020-03-31 13:41:59 UTC  
> Url: https://github.com/boostorg/python/issues/299#issuecomment-606635151  

The use of `return_internal_reference<>` is quite dangerous, as it results in actual references to objects held by-value in your container being wrapped in Python.  
Thus the same rules apply that also apply to iterators in C++: A call to `std::vector<>::push_back()` may cause a reallocation, which invalidates all references, pointers, and iterators.

---

## Comment 2

> Username: NormanT7  
> Created at: 2020-04-08 02:13:53 UTC  
> Url: https://github.com/boostorg/python/issues/299#issuecomment-610712852  

Alright, the behavior I was getting makes sense to me now.   
  
Is it worth adding a note or some additional explanation to the documentation somewhere to help a less experienced user such as myself avoid this? If you think it is worth it, I'd be happy to have a go at it and create a pull request.  
  
Also, is there a straightforward way to get the behavior I was going for (see python code at the end of the example I gave) in the Python interface without using return_internal_reference?

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2020-04-08 11:54:36 UTC  
> Url: https://github.com/boostorg/python/issues/299#issuecomment-610914467  

I believe the issue you were facing manifests itself even in pure C++ applications, thought it's perhaps the combination with Python that obfuscates this enough for users not to be aware of it:   
Dereferencing values from a container and then changing that container may invalidate those references. As soon as you no longer store values but (some sort of) pointers, the problem goes away. And if those pointers are actual `shared_ptr<>` objects, you don't even need to indicate a call policy in your Python wrapper, as the default (by-value passing) will do the right thing.
