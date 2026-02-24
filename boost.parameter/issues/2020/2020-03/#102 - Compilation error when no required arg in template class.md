# #102 - Compilation error when no required arg in template class [Open]

> Username: vincentchabannes  
> Created at: 2020-03-26 12:59:58 UTC  
> Updated at: 2021-12-08 16:02:03 UTC  
> Url: https://github.com/boostorg/parameter/issues/102  

Hi boost developers.  
I have some compilation trouble when I want to use a  BOOST_PARAMETER_MEMBER_FUNCTION with no required argument in a template class. There is no issue for non template class or if I add one require arg.  
This code can reproduce my issue (tested with several version of boost 1.65,1.69,1.70,1.71,1.72)  
  
```  
#include <iostream>  
#include <boost/parameter.hpp>  
  
BOOST_PARAMETER_NAME(arg1)  
  
template <typename T>  
struct somebody  
{  
  BOOST_PARAMETER_MEMBER_FUNCTION(  
                                  (void), static f, tag, (optional (arg1,(int),0))  
                                  )  
  {  
    std::cout << arg1 << std::endl;  
  }  
};  
  
#include <boost/core/lightweight_test.hpp>  
  
int main()  
{  
  somebody<double>::f();  
  somebody<double>::f(4);  
  return boost::report_errors();  
}  
```  
  
The error obtained with clang++ :   
```  
error: template argument for template type parameter must be a type; did you forget 'typename'?  
```  
  
The error obtained with g++ :   
```  
In file included from /usr/include/boost/parameter.hpp:18,  
                 from test_boostparameter.cpp:2:  
test_boostparameter.cpp:9:3: error: type/value mismatch at argument 1 in template parameter list for 'template<class T> template<class Args> struct somebody<T>::boost_param_result_11f'  
   BOOST_PARAMETER_MEMBER_FUNCTION(  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
test_boostparameter.cpp:9:3: note:   expected a type, got 'boost::parameter::aux::argument_pack<somebody<T>::boost_param_params_11f<int> >::type'  
test_boostparameter.cpp:9:3: error: expected ';' at end of member declaration  
   BOOST_PARAMETER_MEMBER_FUNCTION(  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
test_boostparameter.cpp: In function 'int main()':  
test_boostparameter.cpp:21:23: error: cannot call member function 'int somebody<T>::f() [with T = double]' without object  
   somebody<double>::f();  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-08 16:02:03 UTC  
> Url: https://github.com/boostorg/parameter/issues/102#issuecomment-988945467  

I don't think `BOOST_PARAMETER_MEMBER_FUNCTION` is supposed to be used in a class template. There needs to be a separate macro (which is not implemented yet) for that that will add `typename` where needed in the generated code.
