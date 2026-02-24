# #306 - Faq example "wrap a function which takes a function pointer as an argument" not work [Closed]

> Username: hzhangxyz  
> Created at: 2020-04-26 12:28:04 UTC  
> Updated at: 2021-02-20 12:17:20 UTC  
> Closed at: 2021-02-20 12:16:22 UTC  
> Url: https://github.com/boostorg/python/issues/306  

This example code not work on my pc.  
https://www.boost.org/doc/libs/1_72_0/libs/python/doc/html/faq.html  
  
E.cpp  
```  
#include <boost/python.hpp>  
#include <boost/function.hpp>  
#include <string>  
  
using namespace boost::python;  
  
typedef boost::function<void (std::string s) > funcptr;  
  
void foo(funcptr fp)  
{  
  fp("hello,world!");  
}  
  
BOOST_PYTHON_MODULE(E)  
{  
  def("foo",foo);  
}  
```  
  
compile wth `g++ E.cpp -I/usr/include/python3.8 -lpython3.8 -shared -fPIC -o E`python3-config --extension-suffix` -std=c++17 -I../include -lboost_python38`  
  
and run `python -c 'import E; E.foo(print)'`  
  
it say   
```  
Traceback (most recent call last):  
  File "<string>", line 1, in <module>  
Boost.Python.ArgumentError: Python argument types in  
    E.foo(builtin_function_or_method)  
did not match C++ signature:  
    foo(boost::function<void (std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >)>)  
```  
  
environment:  
archlinux, boost 1.72.0, gcc 9.3.0 python 3.8.2

---

## Comment 1

> Username: jordanbriere  
> Created at: 2021-02-20 11:19:04 UTC  
> Url: https://github.com/boostorg/python/issues/306#issuecomment-782609085  

> This example code not work on my pc.  
> https://www.boost.org/doc/libs/1_72_0/libs/python/doc/html/faq.html  
  
You probably figured it out by now, but if you read that page carefully, it actually shows you an example of what "**you can't**" do. 😄   
  
>  The short answer is: "**you can't**". This is not a Boost.Python limitation so much as a limitation of C++. The problem is that a Python function is actually data, and the only way of associating data with a C++ function pointer is to store it in a static variable of the function. The problem with that is that you can only associate one piece of data with every C++ function, and we have no way of compiling a new C++ function on-the-fly for every Python function you decide to pass to foo. In other words, this could work if the C++ function is always going to invoke the same Python function, but you probably don't want that.  
>    
>  If you have the luxury of changing the C++ code you're wrapping, pass it an object instead and call that; the overloaded function call operator will invoke the Python function you pass it behind the object.

---

## Comment 2

> Username: hzhangxyz  
> Created at: 2021-02-20 12:17:20 UTC  
> Url: https://github.com/boostorg/python/issues/306#issuecomment-782619477  

hhhhhhhhhhhhhhhhhhhhhh, so stupid I am
