# #292 - How to wrap classes with move constructors/operators [Closed]

> Username: eugene-kulak  
> Created at: 2020-03-12 14:09:15 UTC  
> Updated at: 2020-03-12 14:22:52 UTC  
> Closed at: 2020-03-12 14:22:36 UTC  
> Url: https://github.com/boostorg/python/issues/292  

Boost version 1.67  
gcc (Ubuntu 9.2.1-9ubuntu2) 9.2.1 20191008  
  
```  
class Test:  
{  
public:  
    Test() {}  
  
    Test(Test&& t)  
    {  
    }  
};  
```  
later this  
```  
#include <boost/python.hpp>  
  
BOOST_PYTHON_MODULE(_utils) {  
    boost::python::class_<Test>("Test");  
}  
```  
cause the following compilation error  
  
> required from ‘void register_wrapper_dimension() [with V = double; T = boost::python::api::object]’  
>    required from here  
> /usr/include/boost/python/object/value_holder.hpp:133:13: error: use of deleted function ‘constexpr Test::Test(const Test&)’  
>   133 |             BOOST_PP_REPEAT_1ST(N, BOOST_PYTHON_UNFORWARD_LOCAL, nil)  
>       |             ^  
>  note: ‘constexpr Test::Test(const Test&)’ is implicitly declared as deleted because ‘Test’ declares a move constructor or move assignment operator  
>     9 | class Test:  
>       |       ^~~~  
>

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2020-03-12 14:14:46 UTC  
> Url: https://github.com/boostorg/python/issues/292#issuecomment-598209623  

If your class isn't copyable, add the `boost::noncopyable` argument to the template arguments of the `class_<...>` . See https://www.boost.org/doc/libs/1_72_0/libs/python/doc/html/reference/high_level_components.html#high_level_components.boost_python_class_hpp.class_template_class_t_bases_hel  
  
for example:  
```  
boost::python::class_<Test, boost::noncopyable>("Test");  
```

---

## Comment 2

> Username: eugene-kulak  
> Created at: 2020-03-12 14:22:36 UTC  
> Updated at: 2020-03-12 14:22:52 UTC  
> Url: https://github.com/boostorg/python/issues/292#issuecomment-598214405  

Thanks @stefanseefeld, `boost::noncopyable` works for me. I don't know how this chapter slipped away from me.  
I will close the issue now.
