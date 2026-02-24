# #105 - boost.python incompatible with -std=c++17 with `noexcept` on g++-7 [Closed]

> Username: Chengifei  
> Created at: 2017-02-08 02:24:06 UTC  
> Updated at: 2019-11-18 16:09:04 UTC  
> Closed at: 2017-02-08 12:42:25 UTC  
> Url: https://github.com/boostorg/python/issues/105  

Hi,  
  
I wanted to post a ticket on svn, but it says 'python USE GITHUB', so I went here...  
The problem can be reproduced on g++-7 20170205 snapshot, and earlier including 201701xx with -std=c++17 enabled and compile the following code:  
```  
#include "boost/python.hpp"  
  
struct A {  
    int do_nothing() noexcept {  
        return 0;  
    }  
};  
  
BOOST_PYTHON_MODULE(test){  
    boost::python::class_<A>("A", boost::python::no_init)  
        .def("get", &A::do_nothing);  
}  
```  
  
And compiler would complain  
```  
./boost/python/detail/invoke.hpp:75:16: error: must use ‘.*’ or ‘->*’ to call pointer-to-member function in ‘f (...)’, e.g. ‘(... ->* f) (...)’  
     return rc(f( BOOST_PP_ENUM_BINARY_PARAMS_Z(1, N, ac, () BOOST_PP_INTERCEPT) ));  
               ~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
However, on g++ 6.3 with -std=c++17, it was all okay. Or if you comment `noexcept` it works fine, too. (I personally think g++-7 would be more standard conforming compared to g++ 6.3, since function signature now comes with exceptions specification) And I believe, that single line is not the real problem, since as I see, invoke has multiple functions to call c++ objects, and it seems to be the higher level code that called a wrong invoke function.  
  
Other part of my project are using C++17 features, so that I'm using g++7 even before release. How can this be resolved?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-02-08 03:27:47 UTC  
> Url: https://github.com/boostorg/python/issues/105#issuecomment-278219091  

I can't claim to understand the full issue yet, but from looking at the line in question (invoke.hpp:75) it seems the issue is that the `A::do_nothing` member pointer isn't recognized as a pointer-to-member. I'll have to play around with g++7 to see why that is, i.e. whether boost's own `is_member_function_pointer` trait yields the wrong value.  
Given that you are already using g++7, perhaps you could do that experiment yourself, and tentatively replace `boost::is_member_function_pointer` by `std::is_member_function_pointer`.  
(It would be a good idea for Boost.Python in general to substitute all the type traits by standard equivalents, when compiling with a C++-11 compiler.)

---

## Comment 2

> Username: Chengifei  
> Created at: 2017-02-08 03:57:44 UTC  
> Url: https://github.com/boostorg/python/issues/105#issuecomment-278222724  

The error had gone!(by changing `boost::` to `std::`) Thanks a lot for your advice, and for internal structure of boost.python. But still, don't know how that's related to `noexcept`... Thanks again!

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-02-08 12:42:25 UTC  
> Url: https://github.com/boostorg/python/issues/105#issuecomment-278318973  

Great. I'v added a new task to do more of that replacement.  
I'm glad to see it was that easy. :-)

---

## Comment 4

> Username: jwakely  
> Created at: 2019-11-18 16:09:04 UTC  
> Url: https://github.com/boostorg/python/issues/105#issuecomment-555085253  

For the record, the problem was due to `noexcept` being part of the function type in C++17, so traits like `is_function` need to be enhanced to handle `noexcept` function types in C++17. Presumably that hadn't been done for the Boost traits.
